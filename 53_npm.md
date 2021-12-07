# npm

## [文法](https://zenn.dev/hedrall/articles/251441f391990f)

```sh
# インストール
npm install パッケージ名
npm i パッケージ名  # 省略形

# バージョンを指定してインストール
npm install パッケージ名@バージョン名

# ローカル環境にインストール
npm i --save-dev パッケージ名
npm i -D パッケージ名  # 省略形

# グローバル環境にインストール
npm i -g パッケージ名  # 省略形

# json形式で表示
npm list --json

# 依存関係の深さを指定して表示
npm list --depth 0
npm list --depth=1

# どのバージョンがあるか調べる
npm info パッケージ名 versions

# 古い依存関係を表示
npm outdated

# package.jsonに記載されていないパッケージをnode_modulesから削除する
npm prune
```

## eslintの設定 -> [参考](https://maku.blog/p/xz9iry9/)

```sh
# インストール
npm install -D eslint

# eslintrcの作成
npx eslint --init

# eslintの実行
npx eslint --ext ts(対象の拡張子) .(パスの指定)
npx eslint 対象ファイル --fix-dry-run
npx eslint 対象ファイル --fix

```

* eslintとprettierの使い分け
  * Prettier -> コードフォーマッター
インデント、改行などの書き方を自動で統一してくれる
  * ESlint -> 静的解析ツール
コードの問題点（バグの可能性があるコードなど）を発見、修正してくれる。
letを使っているが再代入していないので、constを使うべき
（TypeScriptの場合）関数の返り値の型が未定義である
宣言されているが、使っていない変数がある
未定義の変数やモジュールを使用している

## prettierの設定 -> [参考](https://chaika.hatenablog.com/entry/2021/07/21/083000)

* [公式](https://prettier.io/docs/en/cli.html)

```sh
# インストール
npm install -D prettier eslint-config-prettier

# eslintと重複しないようにeslintrcに以下の行を加える
extends: [
+   'prettier',
  ]

# prettierの実行
prettier -c './**/*.{js,jsx,ts,tsx,json,css,scss}' # フォーマットされているかチェック
prettier -l --ignore-path .gitignore './**/*.{js,jsx,ts,tsx,json,css,scss}' # フォーマットされるファイルを表示する
prettier --write --ignore-path .gitignore './**/*.{js,jsx,ts,tsx,json,css,scss}' # フォーマットする

```

* eslintとprettierの使い分け
  * Prettier -> コードフォーマッター  
インデント、改行などの書き方を自動で統一してくれる
  * ESlint -> 静的解析ツール  
コードの問題点（バグの可能性があるコードなど）を発見、修正してくれる。  
    * letを使っているが再代入していないので、constを使うべき  
    * （TypeScriptの場合）関数の返り値の型が未定義である
    * 宣言されているが、使っていない変数がある
    * 未定義の変数やモジュールを使用している

* よく使うライブラリ
  * npm-which

```sh
npm install npm-which
npx npm-which ターゲット
```

* package.json

```json
"dependencies": {
  "a": "3.2.1",
  "b": "~3.2.1", // 3.2.x  明記したところ以下のバージョンがあがることのみ許容
  "c": "^3.2.1", // 3.x.x  一番左の0以外の数字のバージョンを更新しない
  "d": "*"       // x.x.x
}
```
