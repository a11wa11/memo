# yarn

- [yarn](#yarn)
  - [インストール](#インストール)
  - [npmとyarn比較](#npmとyarn比較)

[Yarn](https://www.wakuwakubank.com/posts/307-javascript-yarn/)　とは?

- Facebookが開発したJavaScriptのパッケージマネージャー
- npmと互換性がある
- インストールがより速い
- ネットワーク接続失敗時、自動リトライする
- `yarn.lock`によるバージョン固定化が行われる(`yarn.lock`はyarnでインストールした後に自動生成される)

## インストール

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
| グローバルパッケージ一覧表示 | npm ls -g | yarn global list |
| パッケージ情報 | npm info パッケージ名 | yarn info パッケージ名 |
| 古い依存関係を表示 | npm outdated | yarn outdated |
| キャッシュクリア | npm cache clean | yarn cache clean |
| ローカルパッケージの実行 | npx パッケージ名 | yarn -s run パッケージ名 |
|  |  |  |
