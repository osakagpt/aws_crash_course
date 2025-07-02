# RDS
リレーショナルデータベースサービス(フルマネージド)
- [クラウドフォーメーション経由で操作できる関連リソース一覧](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/AWS_RDS.html)

## RDSのどこがあつい？
オンプレサーバー→クラウドVPS→フルマネージド(RDS)と移り変わりを比較してみる

|           |オンプレ|クラウド(db on EC2,sakura)|フルマネージド(RDS)|
|-----------|--------|-------|-------------|
|OS/DBソフトの<br>アップデート|手動|手動|自動パッチ適用|
|バックアップ|cron設定などで対応|cron?|自動スナップショット|
|スケール対応|手動|AutoScallingなど<br>別機能で対応|自動|
|モニタリング|がんばる||cloudWatchなどとの連携|
|障害対応    |mysql レプリカなど<br>DBの機能でがんばる||デフォルトでマルチAZ|
|金銭コスト  |       |低?|高?|
|必要な知識|物理、OS、DB|OS、DB|AWSサービスの知識|

結論：運用コストやデータ紛失リスクが格段に下がる


## どんなクラウド構成？
- データベースは必ず特定のサブネットグループに紐づけられる
- サブネットグループとは複数(最低2個)のサブネットの集合であり、通常は複数のAZにまたがる
- アプリケーションからはひとつのエンドポイントを通じてのみ認識される(内部でどのようにレプリカインスタンスが作られているかは関与しない) [Amazon RDS マルチ AZ](https://aws.amazon.com/jp/rds/features/multi-az/)
- リードレプリカ(read replica)は読み取り専用のインスタンス。オプションでつけれる。別のエンドポイントをもつ [Amazon RDS リードレプリカ](https://aws.amazon.com/jp/rds/features/read-replicas/)


# マネジメントコンソールからデータベースを作成してみる
- [PostgreSQL データベースを作成して接続する](https://aws.amazon.com/jp/getting-started/hands-on/create-connect-postgresql-db/)

# 最初どのインスタンスタイプを選べばいい？
- しらん


# ユースケース
## 1.インターネットに公開されているPostgreSQL
### ポイント
1. バックアップ機能をどのように達成する？

## 2.EC2にデプロイされたアプリケーションからのみアクセス可能なAuroraDB
### ポイント
1. どの機能を用いてインバウンドのアクセスを制限する？


# 参考文献
[CloudFormation備忘録](https://qiita.com/ej2kd/items/ed17c0f3109590f1d33f)