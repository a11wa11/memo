# terraform

## インストール

- tfenvをインストールし、tfenvからterraformの任意のバージョンをインストール

```sh
brew install tfenv
tfenv install 0.8.1
tfenv install latest
tfenv install latest:^0.8 # 0.8系で最新インストールされる

# 指定したバージョンを使用する
tfenv use 0.8.1
tfenv use latest:^.0.8

# インストールして使用可能なバージョンを一覧表示
tfenv list

# インストール可能なバージョンを表示
tfenv list-remote
```

## 設定

- `.terraform-version`
  - プロジェクトルートに.terraform-versionというファイルを置いておくとそのファイルに書かれたバージョンを優先して利用

### 変数

外部から変数を付与する方法は主に以下の3種類

| No. | 種類 | 例 | 補足 | 上書例 |
|-|-|-|-|-|
| 1 | 環境変数 | `TF_VAR_<NAME>` | `msg`という変数が定義されていると`TF_VAR_msg`で定義可能 | `export TF_VAR_msg="ABC" terraform apply` |
| 2 | 変数ファイル | sample.tfvars | git管理可能 | tfvarsという拡張子で定義されたファイルであれば`-var-file`の引数で指定しなくても自動でtfvars拡張子ファイルが適用される |
| 3 | コマンド引数 | -var <NAME>=<VALUE> | テストや一時的な利用で使える | `terraform apply -var msg="EFG"` |

## 仕様

- `terraform apply`で基本的に実装するが以下の前提がある
  - カレントディレクトリの`tf`ファイルを**すべて**読み込む
  - サブディレクトリの`tf`ファイルは読み込まない

`tfstate`とは

- `tfstate`ファイルはterraformによって構築されたクラウドの状態情報ファイル
  - どのようなりソースが作成されたか、そのリソースのIDや設定内容などを保存している
- backend機能を利用することで、複数のユーザーでterraformを管理する場合、S3バケットにtfstateを保存して１つのtfstateを共有することが可能
  - backend機能なしではstateファイルを実行環境のローカル環境に作成するので複数ユーザーで状態管理をするのが難しい

```terraform
# Backend
terraform {
  required_version = "1.5.6"

  required_providers {
    aws = "5.15.0"
  }

  backend "s3" {
    region         = "ap-northeast-1"
    key            = "任意のファイル名.tfstate"
    dynamodb_table = "任意のDynamoDB名" # オプション。状態ロックをDynamodbで管理することが可能
    bucket         = "任意のバケット名"    # オプション
    encrypt        = true              # オプション
  }
}
```

## 構文

- HCL2とは
  - `H`ashi`C`orp`L`anguage`2` の略称でHashiCorp社の独自言語
  - jsonと似ているが違いがあり、下記jsonとの違い
    - 簡易プログラムが組める
    - コメント(`#`)が記述可能
    - `:`ではなく`=`でキーバリューを指定
    - ヒアドキュメント利用可能
    - `{}`で囲む設定をブロックと呼ぶ
    - ブロックにはいくつかの種類(ブロックタイプ)があり、ブロックタイプによってラベルを指定可能

### ブロックタイプ

|種類|説明|補足|具体例|
|-|-|-|-|
| locals | `変数`: 外部から変更不可 |  | `${local.<変数名>}`で参照 |
| variables | `変数`: 外部から変更可能 | コマンド実行時にオプション、ファイル指定で上書き可能 | `${var.<変数名>}`で参照|
| terraform | terraform本体の設定 | terraformブロックはterraform本体、providerは各サービスのことで対となるような存在 |  |
| provider | AWSやAzure,GCPなどの対象サービスのこと |  |  |
| data | terraform管理外のリソース取得 |  |  |
| resource | terraform管理内のリソース |  |  |
| output | 外部から参照できるようにする値 |  |  |
| import | 手動で作成した既存リソースの取り込み？ | 最終手段？ |  |
| | |  |  |

- リソース参照
  - `resource "aws_vpc" "vpc" {省略}` というリソースが定義されていたら別のリソースから`aws_voc.vpc.id`とラベルをドット(.)区切りで指定して参照できる

#### リソースのインポート

手動作成したリソースなどをterraform管理下に取り込むことができる。大枠は以下

1. 手動作成したリソース名に相当するコードを作成(`アドレス.取り込み先リソース名　`)
  - コードの側だけ作成し、中身は後でインポートで取り込むため空でOK
2. 指定したリソースをインポート(`terraform import 取り込み先リソース名 ターゲットID`)
3. インポートしたリソースを`terraform state show アドレス.リ対象リソース名`で表示し、１のコードに落とし込む

### メタ引数

| No. | 種類 | 例 | 補足 | 上書例 |
|-|-|-|-|-|
| depends_on |  |  |  |  |
| count |  |  |  |  |
| for_each |  |  |  |  |
| lifecycle |  |  |  |  |
| provider |  |  |  |  |

### 拡張機能

vscodeではグラフ化に便利な`graphviz`という拡張機能があり依存関係を可視化できる

### 参考ドキュメント

HCL2とterraformCLI

![HCL2とterraformCLI](https://github.com/a11wa11/memo/assets/46781572/15191928-4634-42d2-8c66-ba6860554df7)

- 公式ドキュメント
  - [HCL2](https://developer.hashicorp.com/terraform/language)
  - [terraform CLI](https://developer.hashicorp.com/terraform/cli) ※`Alphabetical List of Commands`という項目がアルファベット順でコマンドが検索できてわかりやすい
  - [provider(aws)](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

### よく使う事例

- [keys関数](https://developer.hashicorp.com/terraform/language/functions/keys) - キーバリュー形式のキーを表示
- [value関数](https://developer.hashicorp.com/terraform/language/functions/values) - キーバリュー形式のバリューを表示
- [range関数](https://developer.hashicorp.com/terraform/language/functions/range) - 番号リスト作成
- リスト関連
  - [compact関数](https://developer.hashicorp.com/terraform/language/functions/compact) - リストから空・nullを消去する
  - [concat関数](https://developer.hashicorp.com/terraform/language/functions/concat) - リスト同士を繋げて1つのリストにする
  - [flatten関数](https://developer.hashicorp.com/terraform/language/functions/flatten) - リスト同士を繋げてネスト状態や空文字を除去した1つのリストにする
  - [distinct関数](https://developer.hashicorp.com/terraform/language/functions/distinct) - リストから重複した値を消去する
- [length関数](https://developer.hashicorp.com/terraform/language/functions/length) - length確認

```terraform
terraform console
>length(["a", "b"])
```


- プレフィックスリストからIPリストを作成

```terraform
# dataブロックで事前に定義しておくこと
terraform console
>data.aws_ec2_managed_prefix_list.プレフィックスリスト名.entries[*].cidr
```

## コマンド

- 初期化

```sh
terraform init
```

- 確認

```sh
# 現在の状態とコードに定義された状態を比較して、変更が必要な場合に変更内容を表示)
terraform plan
# 特定のリソースのみ確認する
terraform plan -target=指定したいリソース名
# 結果を指定ファイル名へ出力する
terraform plan -out=ファイル名
# デバッグログを有効にして詳細な情報を表示
TF_LOG=DEBUG terraform plan
# クラウド上の現状を反映させる
terraform refresh
terraform apply -refresh-only
# tfstateで管理されているリソースの一覧表示
terraform state list
# tfstateで管理されているリソースの詳細表示
terraform state show アドレス.リソース名
```

- リソース名の変更

```sh
# 注意点としてコードの方も合わせて変更しておくこと
terraform state mv アドレス.変更前リソース名 アドレス.変更後リソース名
```

- リソースのインポート

```sh
# tfstateファイルへリソースのインポートがされるため、コード上は変化なし
terraform import アドレス.リソース名(任意名)　ターゲットID 
```

- デプロイ

```sh
# y/nのプロンプトで最終確認あり
terraform apply
# 特定のリソースのみデプロイ
terraform apply -target=指定したいリソース名
# プロンプトを表示せずに自動的に承認(確認プロンプトが不要)
terraform apply -auto-approve
```

- 削除

```sh
terraform apply -destroy
terraform apply -destroy -target=指定したいリソース名
terraform destory -auto-approve # プロンプトを表示せずに自動的に承認(確認プロンプトが不要)

# terraform管理外から外す
terraform state rm アドレス.変更前リソース名 # コード上にリソースが残っていれば再構築されてしまうので注意
```

- フォーマット

```sh
### フォーマット(カレントディレクトリ) ※変更したファイル名のみ表示 ###
terraform fmt
# フォーマット(変更されるファイル名を事前検証)
terraform fmt -check
# フォーマット(サブディレクトリ含む)
terraform fmt -recursive
# フォーマット(サブディレクトリ含んでフォーマット内容をdiffで表示して事前検証)
terraform fmt -recursive -diff -check
```

- その他

```sh
# 組み込み関数などや変数を試したい時のお試し
terraform console
>local.ターゲット名
ターゲット内容が出力される...
>type(local.default_list)
typeが出力される...
```

## トラブルシューティング

- Terraformの状態ロック
  - `terraform apply`実行時に以下のようなエラーが表示されることがある。  

```sh
Terraform acquires a state lock to protect the state from being written
by multiple users at the same time. Please resolve the issue above and try
again. For most commands, you can disable locking with the "-lock=false"
flag, but this is not recommended.
```

- 状態ロック解除

```sh
# 状態ロック自体を解除
terraform force-unlock xxxxxx(ID)

# 状態ロックを無視してapply
terraform apply -lock=false
```
