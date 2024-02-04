# 今回のゴール
すべての権限を付与されたルートユーザーでEC2インスタンスを起動してローカルからSSH接続できるようになる

# VPC
- [ドキュメント](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)
- [コンソール](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#Home:)

![](images/how-it-works.png)

## キーワード
- リージョン
    - 東京とかテキサスとか
- アベイラビリティゾーン(AZ)
    - データセンターみたいな意味。たとえば東京リージョンは複数のデータセンターを管轄している。

## 設定項目(順番に)
1. VPC
    - LANみたいなやつ。
    - ひとつのリージョンの中につくるが、複数のAZをまたぐことができる
2. サブネット
    - 複数のAZをまたぐことはできない
    - パブリックサブネットとプライベートサブネットに分類できる
    - パブリックとプライベートの違いはインターネットウェイがルートテーブルにあるかどうか
    - EC2などのコンピューティングリソースはこのサブネット上に配置する
3. [ルートテーブル](https://us-east-1.console.aws.amazon.com/vpcconsole/home?region=us-east-1#RouteTables:)
    - サブネットからの外に出る通信をどこに向けて発信するかを定めるルールの集合
    - ひとつのサブネットには必ずひとつのルートテーブルが紐づけられる
        - EC2などのホストごとにルートテーブルがあるわけではないことに注意 
4. インターネットゲートウェイ
    - 作成するリソースのひとつ
    - インターネットとVPC内のリソースをつなぐインターフェイス。デフォルトゲートウェイみたいなイメージ。
    - 作成した後に特定のVPCにアタッチする必要がある
5. [ENI:Elastic Network Interface](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/WindowsGuide/using-eni.html)
    - リソースのひとつでネットワークカードを抽象化したもの
    - プライベートIPとパブリックIPを同時に持つことができる
    - プライベートIPは複数持つこともできる(ひとつのEC2ホストで2つのサービスをデプロイしたいときなど)
6. Elastic IP
    - EC2などのリソースは起動時にENIがアタッチされパブリックIPが割り当てられるがインスタンスが停止するとIP自体も解放されてしまう
    - 常に同じIPアドレスをもっておきたいときにEIPは有用。
7. セキュリティグループ
    - リソースごとに設定するファイヤーウォール
    - 許可ルールを作成することができる。
    - 主にインバウンド通信についての許可を設定する
    - もしある種類のパケットのインバウンド通信を許可したら同種のアウトバウンド通信も自動的に許可される
8. ネットワークACL
    - サブネット単位で設定するファイヤーウォール
    - 拒否ルールを作成することができる
    - インバウンド設定とアウトバウンド設定は別々に設定する必要ある

## 参考文献
- [Amazon VPC とは?](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/what-is-amazon-vpc.html)
- [くろかわこうへいのAWS講座VPC編#1【リージョン、アベイラビリティゾーン、サブネット】](https://www.youtube.com/watch?v=mHi2yFWLz9M)
- [くろかわこうへいのAWS講座VPC編#2【ルートテーブル、インターネットゲートウェイ、NATゲートウェイ、ENI、Elastic IPアドレス】](https://www.youtube.com/watch?v=6muiXYF6CjE)
- [くろかわこうへいのAWS講座VPC編#3【セキュリティグループ、ネットワークACL】](https://www.youtube.com/watch?v=wrHDWQEZOiA)
- [くろかわこうへいのAWS講座VPC編#4【デモ】自宅PCからEC2にログインしよう](https://www.youtube.com/watch?v=_LEDOr1Y57o)