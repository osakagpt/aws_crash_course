# クラウドフォーメーション
[What is AWS CloudFormation?](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/Welcome.html)
- AWSリソースをモデル化してprovisionとconfigureしてくれるサービス
    - provision: 生成とか立ち上げ。EC２インスタンス立ち上げ。
    - configure: 初期設定。EC２インスタンスに特定のポリシーをアタッチ
    - 両者は不可分。ふたつあわせてセットアップ。
- 具体的にはテンプレートとよばれるYAML(or Json)ファイルを準備するだけでVPCやEC２インスタンスなどをセットアップしてくれる。
- このように宣言的にリソースをセットアップする手法をIasC(Infrastructure as Code)とよぶ。
    - クラウドフォーメーション(AWS専用)
    - terraform(マルチクラウド対応)
    - AWS/CLIでAPIを叩いたシェルスクリプト

# クラウドフォーメーションのなにがよい？
- インフラをコードで管理できる
    - どんなリソースを生成したかが一目で把握できる
    - バージョン管理もできる
- おなじテンプレートを食わせると必ず同じリソースが同じ状態にセットアップされる
- なにかしらの理由でセットアップに失敗した場合、ロールバックしてすべてのリソースが破棄され最初の状態に戻る

# テンプレートの例
[](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/template-formats.html)
```yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: A sample template
Resources:
  MyEC2Instance: #An inline comment
    Type: "AWS::EC2::Instance"
    Properties: 
      ImageId: "ami-0ff8a91507f77f867" #Another comment -- This is a Linux AMI
      InstanceType: t2.micro
      KeyName: testkey
      BlockDeviceMappings:
        -
          DeviceName: /dev/sdm
          Ebs:
            VolumeType: io1
            Iops: 200
            DeleteOnTermination: false
            VolumeSize: 20
```
主役は`Resources`!!!

# リソースってなに
- 特定のEC２インスタンスはリソース？
- 特定のIAMユーザーはリソース？
- 特定のIAMポリシーはリソース？
- 特定の信頼ポリシーはリソース？

ARNという識別子(URLに似た文字列)で一意にあらわせるもの
もはやARNでないものを探すほうがむずかしい

# ARN命名規則
[Amazon リソースネーム (ARN)](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/reference-arns.html)
```テンプレ
arn:<partition>:<service>:<region>:<account-id>:<resource-id>
arn:<partition>:<service>:<region>:<account-id>:<resource-type>/<resource-id>
arn:<partition>:<service>:<region>:<account-id>:<resource-type>:<resource-id>
```

### partitionの例
aws, aws-cn

### serviceの例
ec2, iam, s3

#### ややこしいと思ったポイント
- vpcリソースはec2サービスに属している
- バケットポリシーはIAMサービスではなくS3サービスに属している
こういうの多そう

### regionの例
us-east-1, ap-northeast-3

### resource-typeの例
ec2-instance, s3-bucket

# ハンズオン
- テンプレートyamlを0から記述するのはアホらしい
    - ウェブに転がってるお手本スニペットを利用
    - editorの拡張機能で保管してもらう
    - ChatGPTやGithub Copilotにたよる
    - 最終確認として、CloudFormation公式ドキュメントと格闘する
        - [VPC](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html)
        - [EC2インスタンス](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-instance.html)
        - [S3バケット](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-s3-bucket.html)

## CASE 1: 以下の条件を満たすS3バケットを１つ生成
- オブジェクトをパブリックから読むことができる
- オブジェクトをパブリックから書くことができない

### 参考URL
- [2023年 4月から順次、S3 の ブロックパブリックアクセスが デフォルトで有効になります。](https://blog.serverworks.co.jp/s3_bucket_public_access_block_202204)

## CASE 2: 以下の条件を満たすEC2インスタンスを１つ生成
- インスタンスタイプは無料枠のやつ、OSなんでもよい
- パブリックサブネット内に配置し、Elastic IPをひとつ付与
- 適切な鍵をもつパブリックからのSSHアクセスのみ許可

## CASE 3: EC2インスタンスのログイン認証時に用いるキーペアの生成
注意点：クラウドフォーメーションで新しいキーペアを作るときはSystems Managerというサービスを利用するらしい。つまりこのサービスへのAPIを叩く権限をクラウドフォーメーションに持たせる必要がある。これを怠るとスタック生成には失敗するけどキーは生成できる、そしてロールバックに失敗するという面倒な事態になる

### 参考URL
- [キーペアを作成する](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/create-key-pairs.html)