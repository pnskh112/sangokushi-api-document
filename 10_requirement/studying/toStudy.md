# Action Items

<!-- TOC -->

- [Action Items](#v13-action-items)
  - [Requirement list](#requirement-list)
  - [logicDesign論理設計](#logic-design論理設計)
  - [physicalDesign物理設計](#Physical-Design論理設計)
  - [logicDesign論理設計](#logic-design論理設計)


<!-- /TOC -->

## Requirement list

### Arrangement
主に取り決めることは下記の通り
- 論理設計
  - DB設計
- 物理設計
※DB設計について、勉強しながら設計していく。
勉強したこともこちらにまとめていく

### logicdesign論理設計
- 正規化
- ER図

### PhysicalDesign物理設計
- サーバ
- ストレージ

### DB設計について
- 情報はデータと文脈を合成して生まれる<br>
システムとは、このデータと情報の区別という観点から見た場合、<br>
ユーザーがシステムにデータを登録し、さらにそれを引き出して情報を作り出す<br>
という一連のサイクルの中に位置付けることもできる<br>

```uml
@startuml
title システムのサイクル

skinparam ParticipantPadding 10
skinparam BoxPadding 10

actor "ユーザー" as user
actor "ユーザー" as user2
database "DBMS"

    user -> DBMS: 登録
    DBMS -> (情報): 抽出(観点や文脈)
    (情報) -> user2

@enduml
```

