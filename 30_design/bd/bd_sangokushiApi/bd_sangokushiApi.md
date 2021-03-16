<!-- TOC -->

- [概念設計](#Conceptualdesign)
  - [概念の洗い出し](#概念（実体）の洗い出し)
  - [概念のパズル化](#概念のパズル化)

<!-- /TOC -->

## ConceptualDesign

### 概念（実体）の洗い出し

- 武将
- 名前
- 人物像
- 武器・兵馬
- 役職
- 国
- (家系図)
- 時代区分
- 年代
- 名言・エピソード
- 趣味
- 画像
- 領地
- 戦
- 行年没年

家系図は今回はみおくりとする
（画像とかでやるのがいいかも？）

### 概念のパズル化

- A の B（所有） ex) 私（A）のパソコン（B）
- A の B（属性） ex) ティッシュ（A）の香り（B）
- A が B する（主述） ex) タクシー（A）が止まる（B）
- B を A する（動詞-目的語） ex) 客（B）を乗せる（A）
- A があるから B がある（存在理由） ex) 猫（A）がいるから鳴き声（B）がある

この場合はすべて以下のような箱に入る形に

```uml
@startuml
rectangle A {
    rectangle B {
    }
}
@enduml
```

- パズルに当てはめられない場合は、何か概念（実体 entity）足りないことを意味する。
- 具体的に登場する場面をイメージしながら概念のパズルは全て実体で考えていく
- サロゲートキーについては、`今回は`下記の観点から入れるようにする。
  - 誰が見ても主キーとわかる
  - 主キー周りの仕様変更に強い
  - 最近のフレームワークを使った開発はデフォルト主キーが ID になっている。
- 各エンティティはリソースとイベントに分類できる

### 概念スキーマを元に作成した ER 図

```uml
@startuml
title ER図

entity 時代区分{
  * 時代区分コード
  * 国コード<<FK>>
  --
  * 時代区分名
  * 年代
}

entity 国{
  * 国コード
  --
  * 国名
}

entity 武将{
  * 武将コード
  * 国コード<<FK>>
  --
  * 武将名
  * 字
  * 人物像
  * 趣味
  * 行年・没年
}

entity 役職{
  * 役職コード
  * 武将コード<<FK>>
  * 国コード<<FK>>
  * 時代区分コード<<FK>>
  --
  * 役職名
  * 役職説明
}

entity 画像{
  * 画像コード
  * 武将コード<<FK>>
  --
  * 商品コード
}

entity 武器兵馬{
  * 武器兵馬コード
  * 武将コード<<FK>>
  --
  * 武器兵馬名
  * 武器兵馬説明
}

entity 領地{
  * 領地コード
  * 国コード<<FK>>
  * 武将コード<<FK>>
  --
  * 領地名
  * 領地説明
}

entity 戦{
  * 戦コード
  * 領地コード<<FK>>
  * 時代区分コード<<FK>>
  --
  * 戦
  * 概要
}

entity 名言エピソード{
  * 名言エピソードコード
  * 戦コード<<FK>>
  * 武将コード<<FK>>
  --
  * タイトル
  * エピソード
}

国 ||..|{ 役職
国 ||..|{ 領地
国 ||..|{ 武将
国 ||..|{ 時代区分
時代区分 ||..|| 役職
武将 ||..|{ 役職
武将 ||..|| 画像
武将 ||..o{ 武器兵馬
武将 ||..o{ 領地
領地 ||..|{ 戦
時代区分 ||..|{ 戦
戦 ||..|{ 名言エピソード
武将 ||..|{ 名言エピソード
@enduml
```

### 概念スキーマを元に作成した ER 図

テーブル定義


- KINGDUMS（国テーブル）

| Name | Field | Type        | Nullable | Default | Remarks |
| ---- | ----- | ----------- | -------- | ------- | ------- |
| 国コード | ID    | varchar(5)  | N/A      | -       |         |
| 国名   | NAME  | varchar(20) | N/A      | -       |         |

- Constraints

| Field | Key     | Extra | Description |
| ----- | ------- | ----- | ----------- |
| ID    | PRIMARY | N/A   |             |

- WARLOADS（武将テーブル）

| Name  | Field       | Type          | Nullable | Default | Remarks |
| ----- | ----------- | ------------- | -------- | ------- | ------- |
| 武将コード | ID          | varchar(10)    | N/A      | -       |         |
| 国コード  | KINGDUMS_ID | varchar(5)    | N/A      | -       |         |
| 武将名   | NAME        | varchar(20)   | N/A      | -       |         |
| 字名    | AZANA       | varchar(20)   | N/A      | -       |         |
| 人物像   | personality | varchar(1000) | N/A      | -       |         |
| 趣味    | interest    | varchar(500)  | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| KINGDUMS_ID | FOREIGN | REFERENCES KINGDUMS (ID) ON DELETE SET NULL | 外部キー        |

- PERIODS（時代区分テーブル）

| Name    | Field       | Type        | Nullable | Default | Remarks |
| ------- | ----------- | ----------- | -------- | ------- | ------- |
| 時代区分コード | ID          | varchar(10)  | N/A      | -       |         |
| 国コード    | KINGDUMS_ID | varchar(5)  | N/A      | -       |         |
| 時代区分名   | NAME        | varchar(50) | N/A      | -       |         |
| 年代      | AGE         | VARCHAR(20) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| KINGDUMS_ID | FOREIGN | REFERENCES KINGDUMS (ID) ON DELETE SET NULL | 外部キー        |

- POSITIONS（役職テーブル）

| Name    | Field       | Type         | Nullable | Default | Remarks |
| ------- | ----------- | ------------ | -------- | ------- | ------- |
| 役職コード   | ID          | varchar(5)   | N/A      | -       |         |
| 武将コード   | WARLOADS_ID | varchar(10)  | N/A      | -       |         |
| 国コード    | KINGDUMS_ID | varchar(5)   | N/A      | -       |         |
| 時代区分コード | PERIODS_ID  | varchar(10)   | N/A      | -       |         |
| 役職名     | NAME        | varchar(20)  | N/A      | -       |         |
| 役職説明    | EXPLANATION | varchar(500) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| KINGDUMS_ID | FOREIGN | REFERENCES KINGDUMS (ID) ON DELETE SET NULL | 外部キー        |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |
| PERIODS_ID  | FOREIGN | REFERENCES PERIODS (ID) ON DELETE SET NULL  | 外部キー        |

- TERRITORIES（領地テーブル）

| Name  | Field       | Type        | Nullable | Default | Remarks |
| ----- | ----------- | ----------- | -------- | ------- | ------- |
| 領地コード | ID          | varchar(10)  | N/A      | -       |         |
| 武将コード | WARLOADS_ID | varchar(20) | N/A      | -       |         |
| 国コード  | KINGDUMS_ID | varchar(5)  | N/A      | -       |         |
| 領地名   | name        | varchar(20) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| KINGDUMS_ID | FOREIGN | REFERENCES KINGDUMS (ID) ON DELETE SET NULL | 外部キー        |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |

- ARMS（武器兵馬テーブル）

| Name    | Field       | Type         | Nullable | Default | Remarks |
| ------- | ----------- | ------------ | -------- | ------- | ------- |
| 武器兵馬コード | ID          | varchar(10)   | N/A      | -       |         |
| 武将コード   | WARLOADS_ID | varchar(20)  | N/A      | -       |         |
| 武器兵馬名   | name        | varchar(100) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |

- IMAGES（画像テーブル）

| Name  | Field       | Type        | Nullable | Default | Remarks |
| ----- | ----------- | ----------- | -------- | ------- | ------- |
| 画像コード | ID          | varchar(5)  | N/A      | -       |         |
| 武将コード | WARLOADS_ID | varchar(20) | N/A      | -       |         |
| 画像パス  | PATH        | varchar(100) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |

- BATTLES（戦テーブル）

| Name    | Field      | Type        | Nullable | Default | Remarks |
| ------- | ---------- | ----------- | -------- | ------- | ------- |
| 戦コード    | ID         | varchar(10)  | N/A      | -       |         |
| 領地コード   | ID         | varchar(10)  | N/A      | -       |         |
| 時代区分コード | PERIODS_ID | varchar(10)  | N/A      | -       |         |
| 戦名      | NAME       | varchar(20) | N/A      | -       |         |
| 概要      | OVERVIEW       | varchar(500) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| KINGDUMS_ID | FOREIGN | REFERENCES KINGDUMS (ID) ON DELETE SET NULL | 外部キー        |
| PERIODS_ID  | FOREIGN | REFERENCES PERIODS (ID) ON DELETE SET NULL  | 外部キー        |

- WITTICISMS（名言エピソードテーブル）

| Name       | Field       | Type          | Nullable | Default | Remarks |
| ---------- | ----------- | ------------- | -------- | ------- | ------- |
| 名言エピソードコード | id          | varchar(5)    | N/A      | -       |         |
| 戦コード       | id          | varchar(10)    | N/A      | -       |         |
| 武将コード      | WARLOADS_ID | varchar(20)   | N/A      | -       |         |
| タイトル       | TITLE       | varchar(100)  | N/A      | -       |         |
| エピソード      | EPISODE     | varchar(1000) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| BATTLES_ID  | PRIMARY | N/A                                         |             |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |

