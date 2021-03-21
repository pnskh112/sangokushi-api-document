<!-- TOC -->

- [API設計](#apiDesign)
    - [Overview](#overview)
    - [Referring link](#referring-link)
    - [URL](#url)
    - [Header](#header)
        - [JSON pattern](#json-pattern)
        - [POST Sample](#post-sample)
        - [Multi part pattern](#multi-part-pattern)
    - [Error Message](#error-message)
        - [Error Message Layout](#error-message-layout)
    - [Parameter Explanation](#parameter-explanation)
        - [How to sort](#how-to-sort)
        - [How to read Parameter table](#how-to-read-parameter-table)
    - [Treatment Plan](#treatment-plan)
        - [POST /patients/daily-treatment-plan](#post-patientsdaily-treatment-plan)
        - [GET /patients/daily-treatment-plan](#get-patientsdaily-treatment-plan)
        - [GET /patients/monthly-treatment-plan](#get-patientsmonthly-treatment-plan)

<!-- /TOC -->



### apiDesign

#### Overview
- 本ドキュメントでは、API インターフェースの仕様書に関して記載する。


## Referring link
- [日時形式](http://www2.airnet.ne.jp/sardine/docs/NOTE-datetime-19980827.html)
- [認証形式](https://docs.google.com/presentation/d/1Y_oaBuRwniHzDpBqgOze6nU380-bF5CBfYf4MWIRl4Y/edit?usp=sharing)

## URL
- `https://{FQDN}/api/v1/` がAPI のURLになるようにする。

## Header
- HTTP ヘッダ情報は下記の通りとなる。

### JSON pattern

- R が利用する共通鍵ハッシュ値には、 `` でのハッシュ値をヘッダーに渡す。
- タブレットが利用する `GET /tablets/registration` には`X-TabletId`は不必要

| key              | description                    | mandatory |
|:-----------------|:-------------------------------|:----------|
| Accept           | application/json               | yes       |
| Content-Type     | application/json;charset=UTF-8 | yes       |
| Timezone         | +09:00 or Asia/Tokyo           | no        |

### POST Sample

```sh
chcp 65001
curl -v -X POST \
    -H "Accept: application/json"  \
    -H "Content-Type: application/json;charset=UTF-8"  \
    -d '{
          "xxxx": "xxx"
    }' \
    https://{fqdn}/api/
```

### Multi part pattern

| key          | description         | mandatory |
|:-------------|:--------------------|:----------|
| Content-Type | multipart/form-data | yes       |

## Error Message
- エラーメッセージに関する説明を記載する。

### Error Message Layout

**JSON:**  

```json
{
    "code" : "400-001",
    "title" : "BadRequestException",
    "message" : "Given parameter ({field name} : {value}) was invalid format."
}
```

**Message list:**  

| HTTP Status | Code    | Title                           | Message                                                                                                                       |
|:------------|:--------|:--------------------------------|:------------------------------------------------------------------------------------------------------------------------------|
| 400         | 400-001 | BadRequestException             | Given parameter ({field name} : {value}) was invalid format.                                                                  |
| 403         | 403-001 | ForbiddenException              | Invalid authorization value in HTTP header.                                                                                   |
| 404         | 404-001 | NotFoundException               | Given parameter ({field name} : {value}) was not found.                                                                       |
| 405         | 405-001 | MethodNotAllowedException       | Attempted to use an unauthorized method.For example, if a URI that is not allowed to use the POST method is accessed by POSTÎ |
| 409         | 409-001 | AlreadyExistsException          | The api could not store, since the same data relevant to given parameter already existed in database.                         |
| 409         | 409-002 | AlreadyDeletedException         | The relevant data is already deleted.                                                                                         |
| 413         | 413-001 | PayloadTooLargeException        | The payload is too large.                                                                                                     |
| 415         | 415-001 | UnsupportedContentTypeException | The Content-Type in http hearder is not supported.                                                                            |
| 500         | 500-001 | InternalServerErrorException    | The api can not return http response because an internal server error occurred.                                               |
| 501         | 501-001 | NotImplementedException         | The requested method is not implemented on this server.                                                                       |

## Parameter Explanation
- 各種パラメータの関する説明を記載する。

### How to sort
- ソート条件の指定方法は、カンマ区切りで複数指定可能とする。

```
https://{FQDN}/api/page?sort=sort=modified,-patientid,tabletname,serialnumber
```

- URLが上記の場合、SQL は以下のようになる。

```SQL
ORDER BY modified ASC ,patientid DESC, tabletname ASC, serialnumber ASC
```

### How to read Parameter table

| Parameter  | Type   | Size     | Mandatory        | Search     | Default                              | Remarks | Format           |
|:-----------|:-------|:---------|:-----------------|:-----------|:-------------------------------------|:--------|:-----------------|
| Key の名前 | 値の型 | 値の長さ | 値の指定が必須か | 検索可能か | 値を指定しなかった場合に格納される値 | 別名    | 値の詳細な形指定 |

#### Example

Ref: https://docs.microsoft.com/ja-jp/azure/architecture/best-practices/api-design

## Warload
- 治療計画に関する API 群

### POST //Warload

| Name             | Method | Content-Type                   |
|:-----------------|:-------|:-------------------------------|
| 患者情報登録依頼 | POST   | application/json;charset=UTF-8 |

**Process:**  
1. 指定された**karteNumber**と**x-clinic-id**, **x-organization-id**で他の治療計画情報が存在していないかを確認する。
1. 同日のデータが存在した場合はデータを上書き更新する。
    1. 最新のデータを確認し、 `IS_REQUIRED_SIGN` をAPI側で判定して更新する。
        1. `D_INJURIES.RESULT_STATUS` が、前回と異なる場合、サインが必要になる。
1. 治療計画情報を**MONTHLY_TREATMENT_P**テーブルと**DAILY_TREATMENT_P**に登録する。
1. 初診の際は必ずサインが必要になる。

**Path parameters:**  
- N/A

**Querystring:**  
- N/A

**Request body:**  

```json
{
    "karteNumber" : "123",
    "modifiedByCrmStaffId": "1",
    "insuranceType" : "健康保険",
    "created" : "2020-10-02T11:11:11+09:00",
    "modified" : "2020-10-02T11:11:11+09:00",
    "injuries": [
        {
            "id" : "e61a7c5a-3b71-4205-b0d7-dc0b127g2a20",
            "resultStatus" : "continuous",
            "resultDate" : "2020-10-02T11:11:11+09:00",
            "firstCheckUpDate" : "2020-10-02T11:11:11+09:00",
            "injuredDate" : "2020-09-01T11:11:11+09:00",
            "injuryType" : "打",
            "injuryPart" : "頭",
            "injuryPartDetail" : "頭",
            "injuryPartCode" : "1004",
            "injuryPartRefCode" : "103",
            "injuryCause" : "home got hit by a suitcase around a month ago",
        }
    ],
}
```

- 説明

| Parameter                    | Type   | Size | Mandatory | Search | Default | Remarks               | Format                                                               |
|:-----------------------------|:-------|:-----|:----------|:-------|:--------|:----------------------|:---------------------------------------------------------------------|
| karteNumber                  | string | N/A  | yes       | N/A    | N/A     | PATIENTS.KARTE_NUMBER | 半角数値                                                             |
| modifiedByCrmStaffId         | string | 250  | yes       | N/A    | N/A     | 編集者のID            | 文字列                                                               |
| insuranceType                | string | 10   | no        | N/A    | N/A     | 保険種類              | 固定値（健康保険, 自賠責, 労災）                                     |
| created                      | string | N/A  | no        | N/A    | now()   | 作成日時              | YYYY-MM-DDThh:mmTZD<br>(例 1997-07-16T19:20:00+09:00)<br>(UTC+09:00) |
| modified                     | string | N/A  | no        | N/A    | now()   | 最終更新日時          | YYYY-MM-DDThh:mmTZD<br>(例 1997-07-16T19:20:00+09:00)<br>(UTC+09:00) |
| injuries[].resultStatus      | string | 20   | yes       | N/A    | N/A     | 転帰（内容）          | continuous:継続,done:治癒,stop:中止,switched:転医                    |
| injuries[].resultDate        | string | N/A  | no        | N/A    | N/A     | 終了日時              | YYYY-MM-DDThh:mmTZD<br>(例 1997-07-16T19:20:00+09:00)<br>(UTC+09:00) |
| injuries[].firstCheckUpDate  | string | N/A  | yes       | N/A    | N/A     | 初検日                | YYYY-MM-DDThh:mmTZD<br>(例 1997-07-16T19:20:00+09:00)<br>(UTC+09:00) |
| injuries[].injuredDate       | string | N/A  | yes       | N/A    | N/A     | 負傷日                | YYYY-MM-DDThh:mmTZD<br>(例 1997-07-16T19:20:00+09:00)<br>(UTC+09:00) |
| injuries[].injuryType        | string | N/A  | yes       | N/A    | N/A     | 部位分類              | 全角文字                                                             |
| injuries[].injuryPart        | string | N/A  | yes       | N/A    | N/A     | 部位名                | 全角文字                                                             |
| injuries[].injuryPartDetail  | string | N/A  | yes       | N/A    | N/A     | 部位詳細              | 全角文字                                                             |
| injuries[].injuryPartCode    | string | N/A  | yes       | N/A    | N/A     | 部位コード            | 半角数字                                                             |
| injuries[].injuryPartRefCode | string | N/A  | yes       | N/A    | N/A     | 参照用コード          | 半角数字                                                             |
| injuries[].injuryCause       | string | N/A  | no        | N/A    | N/A     | 負傷原因              | 全角文字                                                             |

**Response success:**  

- サンプルオブジェクト  

```JSON
{
    "id" :  "a1c2bc5a-2a77-4205-b0d7-cc0b227f2e19"
}
```

- 説明

| JSON Key | Type   | Size | Mandatory | Sort | Note               | Format |
|:---------|:-------|:-----|:----------|:-----|:-------------------|:-------|
| id       | string | N/A  | yes       | no   | TREATMENT_PLANS.ID | UUIDv4 |

**Response failure:**  

| HTTP Status | Title                           |
|:------------|:--------------------------------|
| 400         | BadRequestException             |
| 404         | NotFoundException               |
| 405         | MethodNotAllowedException       |
| 413         | PayloadTooLargeException        |
| 415         | UnsupportedContentTypeException |
| 500         | InternalServerErrorException    |
| 501         | NotImplementedException         |

