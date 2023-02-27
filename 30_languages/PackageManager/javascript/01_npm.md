# npm

- [npm](#npm)
  - [文法](#文法)
    - [インストール](#インストール)
    - [確認](#確認)
    - [package.json](#packagejson)
    - [package-lock.json](#package-lockjson)
  - [eslintの設定](#eslintの設定)
  - [prettierの設定](#prettierの設定)
  - [husky](#husky)
  - [よく使うライブラリ](#よく使うライブラリ)
  - [Yarn](#yarn)
    - [インストール](#インストール-1)
  - [npmとyarn比較](#npmとyarn比較)

## [文法](https://zenn.dev/hedrall/articles/251441f391990f)

### インストール

```sh
# 基本インストール
npm install パッケージ名

# 応用インストール
npm i パッケージ名          # 省略形
npm ci                    # clean-install package-lock.jsonは更新されない(package-lock.jsonを元にインストール)
npm install               # package.jsonに書かれているパッケージをインストール、package-lock.jsonは更新することがある
npm install --production  # dependenciesに書かれているパッケージのみインストール

# バージョン指定インストール
npm install パッケージ名@バージョン名

# ローカル環境にインストール
npm i --save-dev パッケージ名   # devDependenciesに追加したいとき
npm i -D パッケージ名           # 省略形
npm install --save パッケージ名 # dependenciesに追加したいとき

# グローバル環境にインストール
npm i -g パッケージ名  # 省略形
```

### 確認

```sh
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

# キャッシュクリア
npm cache clean 
npm cache clean --force

# package.jsonに記載されていないパッケージをnode_modulesから削除する
npm prune

# npmのアップデート
npm update -g npm
```

### package.json

- `npm install`によってインストールすべきパッケージのバージョンの範囲(とその他もろもろ)

- 見方

```json
"dependencies": {
  "a": "3.2.1",
  "b": "~3.2.1", // 3.2.x  明記したところ以下のバージョンがあがることのみ許容
  "c": "^3.2.1", // 3.x.x  一番左の3以外の数字のバージョンを更新しない
  "d": "*"       // x.x.x
}
```

- @マークの意味 -> [名前空間としてのアットマーク](https://mass-min.com/archives/1018)
  - npmモジュールは、名前が被るのを許可していない。しかしそれだと`assert`や`cli`のような誰しつける名前は早い者勝ちで名付けられないことになる。
  - そこでnpmでは、「@」と「/」の間にユーザーネーム(or 組織名)を入れて名前空間とし、その後ろにモジュール名をつける「scoped packages」形式を許可しており、以下のような指定方法になる。

```json
"dependencies": {
    "@aws-cdk/assert": "^1.9.0",   # aws-cdkというユーザーネームのassertというモジュール名という意味
    "aws-cdk": "^1.16.0"
}
```

### package-lock.json

- `npm install`によって実際にインストールしたパッケージのバージョンのみが記載されている
  - package-lock.jsonを使うことで`npm install`でインストールした孫依存モジュールを記録しておくことができる
  - 孫依存モジュールの新しいバージョンがリリースされたとしても以前インストールしたバージョンをインストールすることができる

## [eslintの設定](https://maku.blog/p/xz9iry9/)

- コマンド

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

- [eslintrc(設定ファイル)](https://maku.blog/p/j6iu7it/)

```yaml
# 実行時のカレントディレクトリを起点にして、上位のディレクトリの設定ファイル (.eslintrc.*) を探していく。
root: true # trueの指定があると、この検索の振る舞いをそこで停止できる。
env: # 実行環境の指示
# どのようなグローバルオブジェクトを宣言なしで参照可能にするかを ESLint に知らせるための設定。
# 例えば、Web ブラウザ上で動作させる JavaScript コードであれば browser
# Node.js 環境で動作させるコードであれば node を true に設定します
# 内部的には、選択した環境ごとに globals オプションの設定が行われる
  commonjs: true
  es2021: true
  node: true
plugins: # 使用するプラグインの指定
# pluginsはあらかじめnpmでインストールした上で、ここに列挙しておく必要がある。さらに、実際にルールを有効化するには、extends なども指定する必要がある。
  - '@typescript-eslint'
overrides: []
parser: [] # TypeScript コード (.ts、.tsx) を扱えるようにするには、TypeScript 用のパーサー (@typescript-eslint/parser) をインストールして指定する必要がある
  - @typescript-eslint/parser
parserOptions:
  ecmaVersion: 2021 # 12と同様、常に最新を指定するlatestと指定することも可能
extends: # 共有設定の適用
# 複数のルールをまとめたコンフィギュレーション名をする。
# ESLint標準のもの（eslint:recommeded など）以外は、npmパッケージをインストールすることで指定できるようになる。
# 内部のルール設定が重複する場合は、後から指定したものが優先されることに注意。
# 例えば、plugin:@typescript-eslint/recommended は eslint:recommended より後に指定する必要がある（TypeScript 用の設定で上書きする必要があるため）。
  - eslint:recommended 
  - plugin:@typescript-eslint/recommended
rules: # 各ルールの設定
# 多くのケースでは、extends による共有設定で大まかなルール設定を行い、ここで個別ルールを細かく調整する。
  indent: ["error", 2]
  semi: ["error", "always"]
  no-console: warn
```

- eslintとprettierの使い分け
  - Prettier -> コードフォーマッター
インデント、改行などの書き方を自動で統一してくれる
  - ESlint -> 静的解析ツール
コードの問題点（バグの可能性があるコードなど）を発見、修正してくれる。
letを使っているが再代入していないので、constを使うべき
（TypeScriptの場合）関数の返り値の型が未定義である
宣言されているが、使っていない変数がある
未定義の変数やモジュールを使用している

## [prettierの設定](https://chaika.hatenablog.com/entry/2021/07/21/083000)

- [公式](https://prettier.io/docs/en/cli.html)

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

- eslintとprettierの使い分け
  - Prettier -> コードフォーマッター  
インデント、改行などの書き方を自動で統一してくれる
  - ESlint -> 静的解析ツール  
コードの問題点（バグの可能性があるコードなど）を発見、修正してくれる。  
    - letを使っているが再代入していないので、constを使うべき  
    - （TypeScriptの場合）関数の返り値の型が未定義である
    - 宣言されているが、使っていない変数がある
    - 未定義の変数やモジュールを使用している

## husky

gitのcommitやpushといったアクションのタイミングで、コマンドを実行する仕組みを提供するライブラリ

- インストール

```sh
npm install -D husky
npx husky install #最初だけ実行 gitフックを有効にする
```

- 設定

```sh
npx husky add .husky/pre-commit "実行したいスクリプト"
```

## よく使うライブラリ

- npm-which

```sh
npm install npm-which
npx npm-which ターゲット
```

## [Yarn](https://www.wakuwakubank.com/posts/307-javascript-yarn/)

Yarn　とは?

- Facebookが開発したJavaScriptのパッケージマネージャー
- npmと互換性がある
- インストールがより速い
- ネットワーク接続失敗時、自動リトライする
- `yarn.lock`によるバージョン固定化が行われる(`yarn.lock`はyarnでインストールした後に自動生成される)

### インストール

```sh
npm install -g yarn
```

## npmとyarn比較

| 機能 | npm | yarn |
| -- | -- | -- |
| 初期化 | npm init | yarn init |
| package.jsonからインストール | npm install | yarn, yarn install |
| パッケージ削除 | npm uninstall パッケージ名 | yarn remove パッケージ名 |
| パッケージ追加 | npm install パッケージ名 | yarn add パッケージ名 |
| パッケージ追加(devDependencies) | npm install --save-dev --dev パッケージ名 | yarn add --dev パッケージ名 |
| グローバルインストール | npm install -g パッケージ名 | yarn global add パッケージ名 |
| パッケージ削除 | npm uninstall パッケージ名 | yarn remove パッケージ名 |
| ローカルプロジェクトの全パッケージを更新 | npm update | yarn upgrade |
| 指定パッケージを更新 | npm update パッケージ名 | yarn upgrade パッケージ名 |
| scriptを実行 | npm run スクリプト名 | yarn run スクリプト名 |
| パッケージ一覧を表示 | npm ls | yarn list |
| パッケージ情報 | npm info パッケージ名 | yarn info パッケージ名 |
| 古い依存関係を表示 | npm outdated | yarn outdated |
| キャッシュクリア | npm cache clean | yarn cache clean |
| ローカルパッケージの実行 | npx パッケージ名 | yarn -s run パッケージ名 |
|  |  |  |
