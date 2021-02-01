# aws-cli
## aws-cliのインストール手順(mac)
```
# インストール
brew install awscli

# インストール確認
which aws
> /usr/local/bin/aws
aws --version
> aws-cli/2.1.19 Python/3.9.1 Darwin/18.7.0 source/x86_64 prompt/off
```
## AWSの認証情報設定
まず、AWSのどのアカウント、ユーザー、リージョンのものを使うかを設定する
```
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
```
aws configure list

# ユーザーを指定して確認
aws configure list --profile user01
aws s3 ls --profile user01
```

* EC2操作
```
aws ec2 describe-instances
aws ec2 stop-instances --instance-ids インスタンスID
```

* [参考サイト:公式](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config)
* [参考サイト:Qiita](https://qiita.com/reflet/items/e4225435fe692663b705)
