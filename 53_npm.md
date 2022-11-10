# npm

## [文法](https://zenn.dev/hedrall/articles/251441f391990f)

```sh
# インストール
npm install パッケージ名
npm i パッケージ名           # 省略形
npm ci                    # clean-install package-lock.jsonは更新されない(package-lock.jsonを元にインストール)
npm install               # package.jsonに書かれているパッケージをインストール、package-lock.jsonは更新することがある
npm install --production  # dependenciesに書かれているパッケージのみインストール

# バージョンを指定してインストール
npm install パッケージ名@バージョン名

# ローカル環境にインストール
npm i --save-dev パッケージ名 # devDependenciesに追加したいとき
npm i -D パッケージ名  # 省略形
npm install --save パッケージ名 # dependenciesに追加したいとき

# グローバル環境にインストール
npm i -g パッケージ名  # 省略形

# json形式で表示
npm list --json

# 依存関係の深さを指定して表示
npm list --depth 0
npm list --depth=1

# どのバージョンがあるか調べる
npm info パッケージ名 versions
# バージョンを絞り込み
npm info パッケージ名@0.1 version
# 最新バージョンを表示
npm info パッケージ名 version

# 古い依存関係を表示
npm outdated
npm outdated --long # 追加項目表示

# 依存しているパッケージの重複を整理
npm dedupe

# package.jsonに記載されていないパッケージをnode_modulesから削除する
npm prune

# npmのアップデート
npm update -g npm
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

### package.json

* `npm install`によってインストールすべきパッケージのバージョンの範囲(とその他もろもろ)

* 見方

```json
"dependencies": {
  "a": "3.2.1",
  "b": "~3.2.1", // 3.2.x  明記したところ以下のバージョンがあがることのみ許容
  "c": "^3.2.1", // 3.x.x  一番左の3以外の数字のバージョンを更新しない
  "d": "*"       // x.x.x
}
```
* @マークの意味 -> [名前空間としてのアットマーク](https://mass-min.com/archives/1018)
  * npmモジュールは、名前が被るのを許可していない。しかしそれだと`assert`や`cli`のような誰しつける名前は早い者勝ちで名付けられないことになる。
  * そこでnpmでは、「@」と「/」の間にユーザーネーム(or 組織名)を入れて名前空間とし、その後ろにモジュール名をつける「scoped packages」形式を許可しており、以下のような指定方法になる。



```json
"dependencies": {
    "@aws-cdk/assert": "^1.9.0",   # aws-cdkというユーザーネームのassertというモジュール名という意味
    "aws-cdk": "^1.16.0"
}
```

### package-lock.json

* `npm install`によって実際にインストールしたパッケージのバージョンのみが記載されている
  * package-lock.jsonを使うことで`npm install`でインストールした孫依存モジュールを記録しておくことができる
  * 孫依存モジュールの新しいバージョンがリリースされたとしても以前インストールしたバージョンをインストールすることができる




## [Yarn](https://www.wakuwakubank.com/posts/307-javascript-yarn/)
