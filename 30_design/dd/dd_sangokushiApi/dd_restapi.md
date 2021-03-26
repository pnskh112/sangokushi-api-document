<!-- TOC -->

- [API設計](#apiDesign)
    - [Overview](#overview)
    - [Referring link](#referring-link)
    - [URL](#url)
    - [Header](#header)
        - [JSON pattern](#json-pattern)
        - [POST Sample](#post-sample)
    - [Parameter Explanation](#parameter-explanation)
        - [How to sort](#how-to-sort)
        - [How to read Parameter table](#how-to-read-parameter-table)
    - [Treatment Plan](#treatment-plan)
        - [POST /kingdoms](#post-kingdoms)
        - [POST /kingdoms/:kingdoms_id/warloads](#post-kingdoms-warloads)
        - [PUT /warloads/:warloads_id/arms/:arms_id](#patch-warloads-arms)
        - [DELETE /warloads/:warloads_id/arms/:arms_id](#patch-warloads-arms)
        - [PUT /warloads/:warloads_id/images/:images_id](#patch-warloads-arms)
        - [DELETE /warloads/:warloads_id/images/:images_id](#patch-warloads-arms)
        - [POST /warloads/:warloads_id/episodes/:episodes_id/wars](#patch-warloads-arms)
        - [PUT /warloads/:warloads_id/episodes/:episodes_id/wars/:wars_id](#patch-warloads-arms)
        - [DELETE /warloads/:warloads_id/episodes/:episodes_id/wars/:wars_id](#patch-warloads-arms)
        - [PUT /warloads/:warloads_id/positions/:positions_id](#patch-warloads-arms)
        - [PUT /eras/:eras_id/positions/:positions_id/](#patch-eras-arms)
        - [DELETE /eras/:eras_id/positions/:positions_id/](#patch-eras-arms)
        - [PUT /territories/:territories_id/wars/:wars_id/](#patch-territories-arms)
        - [DELETE /territories/:territories_id/wars/:wars_id/](#patch-territories-arms)

<!-- /TOC -->



### apiDesign

#### Overview
- 本ドキュメントでは、API インターフェースの仕様書に関して記載する。

#### API_Prerequisites
- 短く入力しやすい（冗長なパスを含まない）
- 人間が読んで理解できる（省略しない）
- 全て小文字
- 単語はハイフンで繋げる
- 単語は複数形を使う
- エンコード必要な文字を使わない
- サーバ側のアーキテクチャが反映されてない
- 改造しやすい
- ルールが統一されている

* クエリパラメータ

URLの末尾にあるパラメータ
`GET http://api.example.com/users?page=3`

* パスパラメータ

URL中に埋め込まれるパラメータ
`GET http://api.example.com/users/123`

* クエリパラメータとするかどうかの判断基準
- 一意なリソースを表すのに必要かどうか　パスパラメータを利用
- 省略可能かどうか　クエリパラメータを利用
例）検索条件（絞り込み条件）はパスに含めない
　　複数考えられるのでパスには含めず、クエリパラメータで持たせる

## Referring link
- [日時形式](http://www2.airnet.ne.jp/sardine/docs/NOTE-datetime-19980827.html)
- [認証形式](https://docs.google.com/presentation/d/1Y_oaBuRwniHzDpBqgOze6nU380-bF5CBfYf4MWIRl4Y/edit?usp=sharing)

## URL
- `https://{FQDN}` がAPI のURLになるようにする。

## Header
- HTTP ヘッダ情報は下記の通りとなる。

TBD

### POST Sample

```sh
curl -v -X POST \
    -H "Accept: application/json"  \
    -H "Content-Type: application/json;charset=UTF-8"  \
    -d '{
          "xxxx": "xxx"
    }' \
    https://{fqdn}/api/
```

## Error Message
- エラーメッセージに関する説明を記載する。

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


#### Example

Ref: https://docs.microsoft.com/ja-jp/azure/architecture/best-practices/api-design

## Warload
- 治療計画に関する API 群

### post-kingdoms-warloads

| Name             | Method | Content-Type                   |
|:-----------------|:-------|:-------------------------------|
| 武将情報登録処理 | POST   | application/json;charset=UTF-8 |


**Path parameters:**  
- N/A

**Querystring:**  
- N/A

**Request body:**  

```json
{
    "name" : "劉備",
    "azana" : "玄徳",
    "statue" : "『三国志演義』の中心人物。黄巾の乱の鎮圧で功績を挙げ、その後は各地を転戦した。諸葛亮の天下三分の計に基づいて益州の地を得て勢力を築き、後漢の滅亡を受けて皇帝に即位して、蜀漢を建国した。その後の、魏・呉・蜀漢による三国鼎立の時代を生じさせた。中庸を心情とする。",
    "hobby" : "狗馬や音楽、見栄えがある衣服を着ることを好んでいた。",
    "fromTo" : "延熹4年（161年） - 章武3年4月24日（223年6月10日）",
    "arms" : [
        {
            "warloadsId" : "1",
            "name" : "雌雄一対の剣",
            "text" : "劉備（玄徳）愛用の剣。雌雄一対の剣は、桃園の誓いで劉備（玄徳）、関羽、張飛が義兄弟の契りを交わした後、黄巾賊討伐の為に先祖から伝わる剣に似せた二振りの剣。三国志の中では劉備（玄徳）が戦う場面はほとんどないので、雌雄一対の剣を二刀流のように使用したかは定かではない。"
        }
    ],
    "images" : [
        {
            "id" : "1",
            "path" : "/home/work/20210401/aiueo.png",
        }
    ]
}
```

- 説明

| Parameter                    | Type   | Size | Mandatory | Search | Default | Remarks               | Format                                                               |
|:-----------------------------|:-------|:-----|:----------|:-------|:--------|:----------------------|:---------------------------------------------------------------------|
| name                  | string | N/A  | yes       | N/A    | N/A     | PATIENTS.KARTE_NUMBER | 半角数値                                                             |
| azana         | string | 250  | yes       | N/A    | N/A     | 編集者のID            | 文字列                                                               |
| statue                | string | 10   | no        | N/A    | N/A     | 保険種類              | 固定値（健康保険, 自賠責, 労災）                                     |
| hobby                      | string | N/A  | no        | N/A    | now()   | 作成日時              |  |
| fromTo                     | string | N/A  | no        | N/A    | now()   | 最終更新日時          |  |
| arms[].warloadsId      | string | 20   | yes       | N/A    | N/A     | 転帰（内容）          |  |
| arms[].name        | string | N/A  | no        | N/A    | N/A     | 終了日時              | |
| arms[].text  | string | N/A  | yes       | N/A    | N/A     | 初検日                |  |

**Response success:**  

- サンプルオブジェクト  

```JSON
{
    "id": 7,
    "name": "劉備",
    "azana": "玄徳",
    "statue": "『三国志演義』の中心人物。黄巾の乱の鎮圧で功績を挙げ、その後は各地を転戦した。諸葛亮の天下三分の計に基づいて益州の地を得て勢力を築き、後漢の滅亡を受けて皇帝に即位して、蜀漢を建国した。その後の、魏・呉・蜀漢による三国鼎立の時代を生じさせた。中庸を心情とする。",
    "hobby": "狗馬や音楽、見栄えがある衣服を着ることを好んでいた。",
    "newrecord": "延熹4年（161年） - 章武3年4月24日（223年6月10日）"
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

