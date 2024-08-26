# Serverless Framework

## 特徴

Serverless Applicationを構成管理デプロイするためのツール。AWS, GCP, Azure, IBM Cloudなど様々なクラウドプロバイダーに対応している

## インストール

```sh
npm install -g serverless
```

## 基本コマンド

* サービス作成

```sh
serverless create --template aws-nodejs(ランタイム指定) --name my-service(サービス名) --path my-service(パス)
```

* 確認

```sh
# デプロイされる設定内容を確認。serverless.ymlの内容を出力し、実際にどのような設定が適用されるかを確認できる
sls print --stage dev

# デプロイ済のサービスの情報（エンドポイントやリソースなど）を確認
sls info

# デプロイ済バージョンを表示
sls deploy list
# デプロイ済関数とそのバージョンを表示
sls deploy list functions

# ログ確認
sls logs --function 関数名
```

* デプロイ

```sh
sls deploy -v
# --stage または -s     デプロイするステージを指定            例: sls deploy --stage dev
# --region             AWSリージョンを指定                 例: sls deploy --region us-east-1
# --verbose            詳細なデプロイログを表示
# --force              変更がない場合でも強制的にデプロイ
# --package            既に作成したパッケージを使用してデプロイ 例: sls deploy --package ./path/to/package
# --function または -f  特定の関数のみをデプロイ             例: sls deploy --function myFunction
```

* 破棄

```sh
# serverless.ymlがあるパスで実施
sls remove
```
