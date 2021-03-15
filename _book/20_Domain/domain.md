<!-- TOC -->

- [DDD](#DDD)

<!-- /TOC -->

#### DDD
Domain層に何を入れるべきか？
- ビジネスロジック
- アプリケーションで決めている仕様
- オブジェクトはこう定義する、こう動く
- ValueObject
- Entity
- Helper
- Exception
- 静的ロジック（関数で完結するロジック）
- インタフェース（repository）

Domainディレクトリを作り、その中にEntityとRepositoryをおくようにする。
それぞれを呼び出すDomain.tsをつくるのもよい。


