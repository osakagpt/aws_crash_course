# 今回のゴール
ECSサービスを用いてサンプル用アプリケーションをデプロイする

# ECS
- [ドキュメント](https://docs.aws.amazon.com/ja_jp/ecs/)
- [コンソール](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Home:)

# 3つのレイヤー
## provisioning
デプロイ用のスケジューラとのインターフェイス
- management console
- CLI
- SDK
- copilot

## controller
俗にいう「コントロールプレイン」と呼ばれるもので複数のコンテナ上で実行されるアプリケーションをどのようにデプロイしたりコントロールするのかを担う。ECSはここのサービスを指す。競合サービスにEKS(kubernetesを使ったもの)がある。

## capacity
俗にいう「データプレイン」と呼ばれるもので個々のコンテナがどこで起動するかを担う。以下の２つのオプションがあある。
- EC２インスタンス
- fargate
