<!-- TOC -->

- [正規化normalization](#正規化normalization)
    - [正規形の定義](#正規形の定義)
        - [第一正規形](#第一正規形)
        - [第二正規形](#第二正規形)
        - [第三正規形](#第三正規形)
        - [正規形についてのまとめ](#正規形についてのまとめ)

<!-- /TOC -->

## 正規化normalization
- 論理設計において、特に理解を深めておくべき概念が、「正規化（normalization）」及びそれによって作られる「正規形（normal form）」
- 実務での論理設計において本当に頭を悩ませることになるのは、一通りの正規化が「終わった後」である。

### 正規形の定義
- データベースで保持するデータの冗長性を排除し、一貫性と効率性を保持するためのデータ形式。
- 一つの情報が複数のテーブルに存在して無穴データ領域と面倒な更新処理を発生させてしまうことがある（冗長性）
- 冗長なデータ保持をしていると、更新処理のタイムラグによってデータの不整合が発生したり、<br>
そもそもデータを登録することができないようなテーブルを作ってしまうことがある（非一貫性）

#### 第一正規形
- 一つのセルに一つの値が含まれている状態

※ 非正規形の状態のテーブル

扶養者
<br>

| 社員ID | 社員名 | 子  |
|------|-----|----|
| 000A | 加藤  | 達夫 |
|      |     | 慎二 |
| 000B | 藤本  |    |
| 001F | 三島  | 淳  |
|      |     | 陽子 |
|      |     | 清美 |

第１正規形その１
<br>

| 社員ID | 社員名 | 子１ | 子２ | 子３ |
|------|-----|----|----|----|
| 000A | 加藤  | 達夫 | 慎二 |    |
| 000B | 藤本  |    |    |    |
| 001F | 三島  | 淳  | 陽子 | 清美 |

第１正規形その２
<br>

| 社員ID | 社員名 | 子  |
|------|-----|----|
| 000A | 加藤  | 達夫 |
| 000A | 加藤  | 慎二 |
| 000B | 藤本  |    |
| 001F | 三島  | 淳  |
| 001F | 三島  | 陽子 |
| 001F | 三島  | 清美 |
↑このテーブルの主キーは？

基本的にはその２を採用することが望ましい場合が多い。<br>
主キーは一部であってもNULLを含んではならない。

これらのテーブルには２つの問題がある
- 主キーを決められない
- このテーブルが意味的に「社員」と「扶養者」という２つのエンティティを含んでしまっている

第１正規形その３
社員

| 社員ID | 社員名 |
|------|-----|
| 000A | 加藤  |
| 000B | 藤本  |
| 001F | 三島  |

扶養者

| 社員ID | 子  |
|------|----|
| 000A | 達夫 |
| 000A | 慎二 |
| 001F | 淳  |
| 001F | 陽子 |
| 001F | 清美 |

※なぜ一つのセルに複数の値を入れてはダメなのか
- セルに複数の値を許せば、主キーが各列の値を一意に決定できないため
- スカラ値（特定の1行の特定の1列）だけのテーブルならば、主キーは、各列を一意に決めることができる

関数従属性（functional dependency）
X=5と定めれば、Y=10のように、Xに対してYが一つに決まる。これを「YはXに従属する」
と表現する。
「X列の値を決めれば、Y列の値が一つに決まる」という、数学の関数と同じことを意味する。


#### 第二正規形
第一正規形（第２正規形でない）テーブル

| 会社コード | 会社名 | 社員ID | 社員名 | 年齢 | 部署  | 部署名 |
|-------|-----|------|-----|----|-----|-----|
| C001  | A商事 | 000A | 加藤  | 40 | D01 | 開発  |
| C001  | A商事 | 000B | 藤本  | 32 | D02 | 人事  |
| C001  | A商事 | 001F | 三島  | 50 | D03 | 営業  |
| C002  | B化学 | 000A | 斉藤  | 47 | D03 | 営業  |
| C002  | B化学 | 009F | 田島  | 25 | D01 | 開発  |
| C002  | B化学 | 010A | 渋谷  | 33 | D04 | 総務  |

このテーブルは、いずれのセルもスカラ値から構成されているので、問題なく第一正規形。<br>
このテーブルの関数従属が「完全」でないため、第２正規形でない。<br>
主キーの一部の列に対して従属する列がある場合、この関係を`部分関数従属`と呼ぶ。<br>
これに対して主キーを構成するすべての列に従属性がある場合を`完全関数従属`と呼ぶ。

* 第二正規形は部分関数従属を解消し、完全関数従属のみのテーブルを作ること

社員

| 会社コード | 社員ID | 社員名 | 年齢  | 部署コード | 部署名 |
| ----- | ---- | --- | --- | ----- | --- |
| C001  | 000A | 加藤  | 40  | D01   | 開発  |
| C001  | 000B | 藤本  | 32  | D02   | 人事  |
| C001  | 001F | 三島  | 50  | D03   | 営業  |
| C002  | 000A | 斉藤  | 47  | D03   | 営業  |
| C002  | 009F | 田島  | 25  | D01   | 開発  |
| C002  | 010A | 渋谷  | 33  | D04   | 総務  |

会社

| 会社コード | 会社名 |
|-------|-----|
| C001  | A商事 |
| C002  | B化学 |

##### 第二正規形のメリット

上記の第二正規化する前の場合、社員の情報が不明の会社があった場合、<br>
この会社をテーブルに登録できない。ダミー値を入れるのは逃げ手。邪道。<br>
根本的な解決になっていない。<br>
第二正規化した方では、社員の情報が不明でも会社テーブルに登録するだけで対応できる。<br>
この観点から、第二正規化というのは「会社」と「社員」という、<br>
それぞれに異なるレベルの実体（エンティティ）を、きちんとテーブルとしても分離してやる作業とも言える。<br>
<br>

正規化とは可逆的な操作で、正規化の逆操作が結合。

#### 第三正規形
上記の第二正規化した「社員」テーブル「会社」テーブルで起きる不都合

第二正規形（再掲）

社員

| 会社コード | 社員ID | 社員名 | 年齢  | 部署コード | 部署名 |
| ----- | ---- | --- | --- | ----- | --- |
| C001  | 000A | 加藤  | 40  | D01   | 開発  |
| C001  | 000B | 藤本  | 32  | D02   | 人事  |
| C001  | 001F | 三島  | 50  | D03   | 営業  |
| C002  | 000A | 斉藤  | 47  | D03   | 営業  |
| C002  | 009F | 田島  | 25  | D01   | 開発  |
| C002  | 010A | 渋谷  | 33  | D04   | 総務  |


会社

| 会社コード | 会社名 |
|-------|-----|
| C001  | A商事 |
| C002  | B化学 |

社員が一人もいない部署を社員テーブルには登録できない。<br>
社員IDが主キーの一部である以上、そこをNULLのままレコードを登録できない<br>
このような不都合が発生する理由は、「社員」テーブルの中に、関数従属性が残っているためである。<br>
<br>
<br>

* 推移的関数従属

`{部署コード} -> {部署名}`

`{会社コード,社員ID} -> {部署コード}`

が成り立つので、よって、

`{会社コード,社員ID} -> {部署コード} -> {部署名}`

という２段階の関数従属がある。これを`推移的関数従属`という。

第三正規形

社員

| 会社コード | 社員ID | 社員名 | 年齢  | 部署コード |
| ----- | ---- | --- | --- | ----- |
| C0001 | 000A | 加藤  | 40  | D01   |
| C0001 | 000B | 藤本  | 32  | D02   |
| C0001 | 001F | 満島  | 50  | D03   |
| C0002 | 000A | 斉藤  | 47  | D03   |
| C0002 | 009F | 田島  | 25  | D01   |
| C0002 | 010A | 渋谷  | 33  | D04   |


会社

| 会社コード | 会社名 |
| ----- | --- |
| C0001 | A商事 |
| C0002 | B化学 |
| C0003 | C建設 |

部署

| 部署コード | 部署名 |
| ----- | --- |
| D01   | 開発  |
| D02   | 人事  |
| D03   | 営業  |
| D04   | 総務  |

新しく部署を管理するための部署テーブルを独立させることで、<br>
すべてのテーブルについて、非キー列はキー列に対してのみ従属するようになり、<br>
推移的関数従属もなくなる。<br>
一人もいない部署を登録できないという問題は解消される。<br>


#### 正規形についてのまとめ

- 正規化を行う目的は、何よりも更新（データ登録＝INSERTも含む）時の不都合を防ぐためのもの。<br>
データの冗長性を排除して、人間のオペレーションミスによるデータ不整合を防ぐ目的もある。
- 正規化は従属性を見抜くことで可能になる
    - 部分関数従属（第二正規形）
    - 推移的関数従属（第三正規形）
    が存在していれば、まず正規化の対象になる。
- 正規形はいつでも非正規形に戻せる
正規化によって分割されたテーブルは、いつでも非正規化テーブルに復元することができる。<br>
正規化が情報を完全に保存する無損失分解だからである。

- 第三正規形までは、原則として行う。
- 関連エンティティが存在する場合は関連とエンティティが１対１に対応するよう注意する

正規化を行えば行うほど以下のような利点と欠点がある。
- 利点１：データの冗長性が排除され、更新時の不整合を防止できる
- 利点２：テーブルの持つ意味が明確になり、開発者が理解しやすい
- 欠点：テーブルの数が増えるため、SQL文で結合を多用することになり、パフォーマンスが悪化する。



非正規形テーブル
下記の非正規化テーブルの関数従属をまとめ、正規化してみなさい
支社支店商品（非正規化テーブル）

| 支社コード | 支社名 | 支店コード | 支店名 | 商品コード | 商品名  | 商品分類コード | 分類名  |
|-------|-----|-------|-----|-------|------|---------|------|
| 001   | 東京  | 01    | 渋谷  | 001   | 石鹸   | C1      | 水洗用品 |
| 001   | 東京  | 01    | 渋谷  | 002   | タオル  | C1      | 水洗用品 |
| 001   | 東京  | 01    | 渋谷  | 003   | 歯ブラシ | C1      | 水洗用品 |
| 001   | 東京  | 02    | 八重洲 | 002   | タオル  | C1      | 水洗用品 |
| 001   | 東京  | 02    | 八重洲 | 003   | 歯ブラシ | C1      | 水洗用品 |
| 001   | 東京  | 02    | 八重洲 | 004   | コップ  | C1      | 水洗用品 |
| 001   | 東京  | 02    | 八重洲 | 005   | 箸    | C2      | 食器   |
| 001   | 東京  | 02    | 八重洲 | 006   | スプーン | C2      | 食器   |
| 002   | 大阪  | 01    | 堺   | 001   | 石鹸   | C1      | 水洗用品 |
| 002   | 大阪  | 01    | 堺   | 002   | タオル  | C1      | 水洗用品 |
| 002   | 大阪  | 02    | 豊中  | 007   | 雑誌   | C3      | 書籍   |
| 002   | 大阪  | 02    | 豊中  | 008   | 爪切り  | C4      | 日用雑貨 |



- 関数従属
    - 部分関数従属<br>
        `{支社コード} -> {支社名}`      
        `{支社コード,支店コード} -> {支店名}`<br>
        `{支社コード,支店コード,商品コード} -> {商品名,商品分類コード,分類名}`            
        `{商品コード} -> {商品名,商品分類コード}`
    - 推移的関数従属<br>
        `{商品コード} -> {商品分類コード} -> {分類名}`

支社

| 支社コード | 支社名 |
| ----- | --- |
| 001   | 東京  |
| 002   | 大阪  |

支店

| 支社コード | 支店コード | 支店名 |
| ----- | ----- | --- |
| 001   | 01    | 渋谷  |
| 001   | 02    | 八重洲 |
| 002   | 01    | 堺   |
| 002   | 02    | 豊中  |

商品

| 商品コード | 商品名  | 商品分類コード |
| ----- | ---- | ------- |
| 001   | 石鹸   | C1      |
| 002   | タオル  | C1      |
| 003   | 歯ブラシ | C1      |
| 004   | コップ  | C1      |
| 005   | 箸    | C2      |
| 006   | スプーン | C2      |
| 007   | 雑誌   | C3      |
| 008   | 爪切り  | C4      |

商品分類

| 商品分類コード | 分類名  |
| ------- | ---- |
| C1      | 水洗用品 |
| C2      | 食器   |
| C3      | 書籍   |
| C4      | 日用雑貨 |

支社支店商品

| 支社コード | 支店コード | 商品コード |
| ----- | ----- | ----- |
| 001   | 01    | 001   |
| 001   | 01    | 002   |
| 001   | 01    | 003   |
| 001   | 02    | 002   |
| 001   | 02    | 003   |
| 001   | 02    | 004   |
| 001   | 02    | 005   |
| 001   | 02    | 006   |
| 002   | 01    | 001   |
| 002   | 01    | 002   |
| 002   | 02    | 007   |
| 002   | 02    | 008   |

