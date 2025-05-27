# Serverless Framework

- [Serverless Framework](#serverless-framework)
  - [概要](#概要)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
  - [serverless.yml](#serverlessyml)

## 概要

Serverless Applicationを構成管理デプロイするためのツール。AWS, GCP, Azure, IBM Cloudなど様々なクラウドプロバイダーに対応している

[公式ドキュメント](https://www.serverless-dev.com/framework/docs)

## インストール

```sh
npm install -g serverless

# ServerlessFrameworkのプラグインインストール
serverless plugin install -n プラグイン名
serverless plugin install --name プラグイン名
```

## コマンド

- サービス作成

```sh
serverless create --template aws-nodejs(ランタイム指定) --name my-service(サービス名) --path my-service(パス)
```

- 確認

```sh
# デプロイされる設定内容を確認。serverless.ymlの内容を出力し、実際にどのような設定が適用されるかを確認できる
sls print --stage dev

# デプロイ済のサービスの情報（エンドポイントやリソースなど）を確認
sls info

# デプロイ済リストと日付を表示
sls deploy list
# デプロイ済lambda(関数:バージョン)を表示
sls deploy list functions

# ログ確認
sls logs --function 関数名(serverless.ymlで定義されている関数名)

# .serverless配下にデプロイパッケージが作成される
sls package

# 作成したデプロイパッケージから変更セットを作成する場合
aws cloudformation create-change-set \
  --stack-name スタック名 \
  --template-body file://.serverless/cloudformation-template-update-stack.json \
  --change-set-name 任意の変更セット名 \
  --capabilities CAPABILITY_NAMED_IAM

# 作成した変更セットを実行する場合
aws cloudformation execute-change-set --change-set-name 任意の変更セット名
```

- デプロイ

```sh
sls deploy -v
# --stage または -s     デプロイするステージを指定            例: sls deploy --stage dev
# --region             AWSリージョンを指定                 例: sls deploy --region us-east-1
# --verbose または -v   詳細なデプロイログを表示
# --force              変更がない場合でも強制的にデプロイ
# --package            既に作成したパッケージを使用してデプロイ 例: sls deploy --package ./path/to/package
# --function または -f  特定の関数のみをデプロイ             例: sls deploy --function myFunction
# --config または -c    severless.ymlファイル相当パスを指定  例: sls deploy -c serverless.stg.yml
```

- 破棄
  - `sls remove`コマンドが無難だが、serverless.ymlファイルの変遷でサービス名が変わったり、消し忘れがあった場合はcloudformationからスタック削除でも消去可能

```sh
# serverless.ymlがあるパスで実施
sls remove -s dev
```

## serverless.yml

```yml
service: サービス名

# serverlessのバージョン
frameworkVersion: '3'

# awsの詳細の指定
provider:
  name: aws
  runtime: nodejs16.x
  region: ap-northeast-1
  stage: dev # defaultでdev

# aws lambda function
functions:
  関数名:
    handler: dist/lambda.handler
    environment:

# デプロイパッケージの細かい指定をしたい時  https://www.serverless-dev.com/framework/docs/providers/aws/guide/packaging
package:
  include:  # 含めるファイル
    - dist/**
    - env/**
    - node_modules/**
  exclude:  # 除外したいファイル
    - package.json

# 自作変数を定義したい時
custom:
  defaultStage: dev

# プラグイン
plugins:
  - serverless-offline
```
