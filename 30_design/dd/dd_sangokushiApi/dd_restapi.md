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

- Receone が利用する共通鍵ハッシュ値には、 `CRM院ID、CRM会社ID、送信日時、共通鍵` でのハッシュ値をヘッダーに渡す。
- タブレットが利用する `GET /tablets/registration` には`X-TabletId`は不必要

| key              | description                    | mandatory |
|:-----------------|:-------------------------------|:----------|
| Accept           | application/json               | yes       |
| Content-Type     | application/json;charset=UTF-8 | yes       |
| Timezone         | +09:00 or Asia/Tokyo           | no        |
| X-Crm-Shop-Id    | 院ID                           | yes       |
| X-Crm-Company-Id | 会社ID                         | yes       |
| X-Hashed-Key     | 共通鍵ハッシュ値               | no        |
| X-Timestamp      | クライアントデータ送信日時     | no        |
| X-TabletId       | タブレットID                   | no        |

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

