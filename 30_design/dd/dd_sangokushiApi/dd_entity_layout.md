<!-- TOC -->

- [テーブル定義](#テーブル定義)

<!-- /TOC -->


### テーブル定義


- KINGDUMS（国テーブル）

| Name | Field | Type        | Nullable | Default | Remarks |
| ---- | ----- | ----------- | -------- | ------- | ------- |
| 国コード | ID    | varchar(5)  | N/A      | -       |         |
| 国名   | NAME  | varchar(20) | N/A      | -       |         |
| 説明   | NAME  | varchar(250) | N/A      | -       |         |

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
| 人物像   | PERSONALITY | varchar(1000) | N/A      | -       |         |
| 趣味    | INTEREST    | varchar(500)  | N/A      | -       |         |

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
| 領地名   | NAME        | varchar(20) | N/A      | -       |         |

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
| 武器兵馬名   | NAME        | varchar(100) | N/A      | -       |         |

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
| 名言エピソードコード | ID          | varchar(5)    | N/A      | -       |         |
| 戦コード       | ID          | varchar(10)    | N/A      | -       |         |
| 武将コード      | WARLOADS_ID | varchar(20)   | N/A      | -       |         |
| タイトル       | TITLE       | varchar(100)  | N/A      | -       |         |
| エピソード      | EPISODE     | varchar(1000) | N/A      | -       |         |

- Constraints

| Field       | Key     | Extra                                       | Description |
| ----------- | ------- | ------------------------------------------- | ----------- |
| ID          | PRIMARY | N/A                                         |             |
| BATTLES_ID  | PRIMARY | N/A                                         |             |
| WARLOADS_ID | FOREIGN | REFERENCES WARLOADS (ID) ON DELETE SET NULL | 外部キー        |

