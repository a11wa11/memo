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

## 操作コマンド

```sh
# EC2
aws ec2 describe-instances
aws ec2 describe-instance-status
aws ec2 start-instances --instance-ids インスタンスID
aws ec2 stop-instances --instance-ids インスタンスID
aws ec2 describe-vpcs
# AMI　検索
aws ec2 describe-images
aws ec2 describe-images --image-ids ami-XXXXXXXXXXX --output table
aws ec2 describe-images --owners amazon --filters "Name=name,Values=amzn2-ami-hvm-2.0.2020*" --query 'reverse(sort_by(Images, &Name))[].[Name,ImageId,CreationDate]' --output table

# RDS
aws rds describe-db-instances
aws rds describe-db-clusters  --db-cluster-identifier クラスターID
aws rds describe-db-cluster-endpoints

# シークレットマネージャー
aws secretsmanager list-secrets
aws secretsmanager get-secret-value --secret-id シークレット名
# シークレット即時消去
aws secretsmanager delete-secret --secret-id シークレット名 --force-delete-without-recovery --region us-east-1

# パラメーターストア
# パラメーターストアから値だけ取得してテキスト形式で出力する
aws ssm get-parameter --name 'パラメーター名' --query Parameter.Value --output text
# 暗号化パラメーターを取得
aws ssm get-parameter --name パラメーター名 --query Parameter.Value --with-decryption --output text

# ALBのルール表示
aws elbv2 describe-rules --listener-arn arn:aws:elasticloadbalancing:リージョン:アカウント:listener/app/XXXXX/YYYYY/ZZZZZ

# アカウントの確認
aws sts get-caller-identity
```

* [S3](https://qiita.com/uhooi/items/48ef6ef2b34162988295)

```sh
# S3
aws s3 cp コピー元　コピー先
# ディレクトリごとコピー
aws s3 cp ディレクトリ名 s3://バケット名/対象フォルダ名 --recursive
# ディレクトリごと消去
aws s3 rm s3://バケット名/ディレクトリ名/ --recursive
# バケット消去(空でなくても削除)
aws s3 rb s3://バケット名 --force
# バケットの内容をローカルのフォルダと同期する(追加・更新のみで削除されない)
aws s3 sync 対象フォルダ s3://バケット名/パス
# バケットの内容をローカルのフォルダと同期する(削除もされる)
aws s3 sync 対象フォルダ s3://バケット名/パス --delete
# ローカルのファイルをバケットに移動する
aws s3 mv 対象ファイル s3://バケット名/パス
```

* [cloudformation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/using-cfn-describing-stacks.html)
* [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html#cli-aws-cloudformation)

```sh
# 正しく完了したstack
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE
# 削除90日以内含むstack
aws cloudformation list-stacks  | grep スタック名
# ステータスがDELETE_COMPLETE以外のスタックを [StackStatus/LastUpdatedTime/StackName] の形式で表示
aws cloudformation list-stacks --output json | jq -r '.StackSummaries[] | .result = .StackStatus + " " + .LastUpdatedTime + " " + .StackName | .result' | grep -v DELETE_COMPLETE | sort
# 実行中のスタックの情報を表示
aws cloudformation describe-stacks --stack-name スタック名
# 実行中のstack
aws cloudformation describe-stacks | grep スタック名
```

* 値の取得や形式、フィルターに関して

```sh
# フィルターに関して
aws secretsmanager list-secrets --filters Key=name,Value=任意の値
aws ec2 describe-vpcs --region ap-northeast-1 --filters "Name=tag-value,Value=XXX" --query "Vpcs[].CidrBlockAssociationSet[].CidrBloc"
aws ec2 describe-vpcs --region ap-northeast-1 --filters "Name=tag-value,Values=XXX" --query "Vpcs[].CidrBlockAssociationSet[].CidrBlock" --output text

# 特定の値を取得
aws secretsmanager list-secrets --query "値[*].値"
# 出力形式
aws secretsmanager list-secrets --output text
aws secretsmanager list-secrets --output json
aws secretsmanager list-secrets --output yaml

```

* [参考サイト:公式](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)
* [参考サイト:Qiita](https://qiita.com/reflet/items/e4225435fe692663b705)
