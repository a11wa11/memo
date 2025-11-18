# nextjs

- [nextjs](#nextjs)
  - [概要](#概要)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
  - [設定ファイル](#設定ファイル)
  - [開発環境](#開発環境)

## 概要

複数のjs,css,pngファイルなどを1つのファイルにまとめることで、結果的に1つのファイルしか読み込まなくていいようにする

## インストール

```sh
npm install next
npx create-next-app@latest プロジェクト名 # インストールなしでも可能
```

## コマンド

```sh
next dev   # 開発サーバーを起動。 http://localhost:3000 
next build # .nextフォルダに最適化されたファイルが生成
next start # next buildで生成された本番用ビルドを実行

# 差分ビルド
npx webpack --watch

# ビルドの進行状況をリアルタイムで確認できる
npx webpack --progress

# 開発モード デバッグや修正を容易にするための設定が自動で有効になる。ファイルは圧縮されず、人間が読める形で出力されることが一般的
npx webpack --mode development

# 本番モード　最適化が自動的に有効化され、軽量かつ高速なバンドルファイルが生成される
npx webpack --mode production
```

## 設定ファイル

- webpack.config.jsで設定可能

```webpack.config.js
module.export = {
  // エントリーポイント
  entry: "./src/index.js",

  output: {
    // ファイル出力先のパス
    path: `${__dirname}/dist`,
    // 出力ファイル名
    filename: "main.js"
  },
  // developmentとproductionがある
  mode: development
};
```

## 開発環境

- webpack専用のローカルサーバーが構築可能な`webpack-dev-server`がある

```sh
# インストール
npm install webpack-dev-server

# 起動
npx webpack server
```

- `webpack-dev-server`の設定

```webpack.config.js
module.export = {
...
  // ローカルサーバーの設定
  devServer: {
    static: "dist",
    open: true,
  },
};
```
