# aws-cli

## aws-cliのインストール手順(mac)

```sh
# インストール
brew install awscli

# インストール確認
which aws
> /usr/local/bin/aws
aws --version
> aws-cli/2.1.19 Python/3.9.1 Darwin/18.7.0 source/x86_64 prompt/off
```

## AWSの認証情報設定

* まず、AWSのどのアカウント、ユーザー、リージョンのものを使うかを設定する

```sh
aws configure # デフォルト設定
aws configure --profile 名付けたいユーザー名 # ユーザーを作成して設定
> AWS Access Key ID [None]: {アクセスキー(各自)}
> AWS Secret Access Key [None]: {シークレットアクセスキー(各自)}
> Default region name [None]: ap-northeast-1
> Default output format [None]: json　# yamlかjsonかが無難
```

* `--profile ユーザー名`のオプションは省略するとデフォルトの設定で実行される
* AccessKey と SecretAccessKey はIAM画面で発行
  * 注意事項
    * 発行時しかSecretAccessKeyは確認できない
    * 発行時以外SecretAccessKey再確認不可
    * 別にAccessKey発行は可能
* `aws configure`コマンドで認証情報を発行後、ホームディレクトリに`.aws`の隠しフォルダが作成される。
  * AccessKey と SecretAccessKeyは`.aws/credentials`に保存される
  * リージョンと出力形式は`.aws/config`に保存される
  * `aws configure`未設定では`.aws`フォルダが作成されていない

### AWSの認証情報を確認

```sh
aws configure list

# ユーザーを指定して確認
aws configure list --profile user01
aws s3 ls --profile user01

# リージョン登録
aws configure set region us-east-2
```

* 操作コマンド

```sh
aws ec2 describe-instances
aws ec2 describe-instance-status
aws ec2 start-instances --instance-ids インスタンスID
aws ec2 stop-instances --instance-ids インスタンスID
aws ec2 describe-vpcs
aws ec2 describe-images # AMI

aws rds describe-db-instances
aws rds describe-db-clusters  --db-cluster-identifier クラスターID
aws rds describe-db-cluster-endpoints

# シークレットマネージャー
aws secretsmanager list-secrets
aws secretsmanager get-secret-value --secret-id シークレット名

# フィルターに関して
aws secretsmanager list-secrets --filters Key=name,Value=任意の値
aws ec2 describe-vpcs --region ap-northeast-1 --filters "Name=tag-value,Value=XXX" --query "Vpcs[].CidrBlockAssociationSet[].CidrBloc"

# 特定の値を取得
aws secretsmanager list-secrets --query "値[*].値"
# 出力形式
aws secretsmanager list-secrets --output text
aws secretsmanager list-secrets --output json
aws secretsmanager list-secrets --output yaml
```

* s3

```sh
aws s3 cp ディレクトリ名 s3://バケット名/対象フォルダ名 --recursie
```

* パラメーターストア

```sh
# パラメーターストアから値だけ取得してテキスト形式で出力する
aws ssm get-parameter --name 'パラメーター名' --query Parameter.Value --output text
# 暗号化パラメーターを取得
aws ssm get-parameter --name パラメーター名 --query Parameter.Value --with-decryption --output text
```

* [参考サイト:公式](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)
* [参考サイト:Qiita](https://qiita.com/reflet/items/e4225435fe692663b705)
