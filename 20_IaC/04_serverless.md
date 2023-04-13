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

* デプロイ

```sh
sls deploy -v
```

* 破棄

```sh
# serverless.ymlがあるパスで実施
sls remove
```      
