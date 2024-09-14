# nodemon

## 概要

ファイルの変更を監視し、変更が検知されると自動的にサーバーやアプリケーションを再起動する。
手動でサーバーを再起動する手間を省ける

- ファイル変更の監視: プロジェクト内のファイルが変更されると、自動的に再起動する
- カスタム設定: 監視するファイルの種類やディレクトリを指定することができる
- コマンドの自動再実行: 任意のコマンドも nodemon を通して実行し、自動再起動を実現できる

## インストール

```sh
npm install -g nodemon
```

## コマンド

```sh
# app.jsを nodemonで実行
nodemon app.js
# 特定のファイルやフォルダを監視する
nodemon --ext js
# node_modulesフォルダを無視する
nodemon --ignore node_modules/ app.js
```

## 設定

- nodemon.jsonで詳細な設定を保存できる

```nodemon.json
{
  "watch": ["src"],
  "ext": "js,json",
  "ignore": ["node_modules"],
  "exec": "node app.js"
}
```
