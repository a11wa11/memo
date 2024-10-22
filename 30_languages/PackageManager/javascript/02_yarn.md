# yarn

- [yarn](#yarn)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
    - [yarn classic (1.x)](#yarn-classic-1x)
    - [yarn berry(バージョン2以上)](#yarn-berryバージョン2以上)
  - [npmとyarn比較](#npmとyarn比較)

[Yarn](https://www.wakuwakubank.com/posts/307-javascript-yarn/)　とは?

- Facebookが開発したJavaScriptのパッケージマネージャー
- npmと互換性がある
- インストールがより速い
- ネットワーク接続失敗時、自動リトライする
- `yarn.lock`によるバージョン固定化が行われる(`yarn.lock`はyarnでインストールした後に自動生成される)

## インストール

[ドキュメント](https://classic.yarnpkg.com/en/docs/cli/install)

```sh
npm install -g yarn
```

## コマンド

- yarnのメジャーバージョンの調整

```sh
yarn set version classic # yarn1.xをインストール
yarn set version berry   # yarn2.x以上をインストール
yarn set version 3       # yarn3.xをインストール
yarn set version 4       # yarn4.xをインストール
yarn set version stable  # yarn stableバージョンをインストール
```

### yarn classic (1.x)

```sh
# 実行可能ファイルが保存されているディレクトリ（バイナリのパス）を確認
yarn bin
yarn bin パッケージ名
yarn global bin

# インストールオプション
yarn install --verbose         # 冗長オプション
yarn install --production      # package.jsonのdependenciesのみインストール
yarn install --frozen-lockfile # yarn.lockを更新しない
yarn install --ignore-optional # オプションの依存関係をインストールしないようにする

# パッケージの保存先ディレクトリを確認
yarn dir
yarn dir パッケージ名
yarn global dir

# パッケージ一覧
yarn list --depth=0 #依存関係先非表示
yarn list --depth=1 #依存関係先を1段階表示

# 対象パッケージを依存関係先の場合も含めて表示
yarn list --pattern パッケージ名
yarn list --pattern "パッケージ名1|パッケージ名2" # 複数検索の場合

# 対象パッケージが他のどのパッケージから依存されているかを表示
yarn why パッケージ名

# 脆弱性や問題があるか調査
yarn audit

# UIで依存関係の更新を選択できる
yarn upgrade-interactive
yarn upgrade-interactive --latest
```

### yarn berry(バージョン2以上)

```sh
# node_nodulesにインストールするように設定
yarn config set nodeLinker node-modules

yarn install --inline-builds # 冗長オプション
```

## npmとyarn比較

- ※yarn 1.xとして記述

| 機能 | npm | yarn |
| -- | -- | -- |
| 初期化 | npm init | yarn init |
| package.jsonからインストール | npm install | yarn, yarn install |
| package.jsonのdependenciesからインストール | npm install --production | yarn install --production |
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
| ローカルにあるパッケージをpublishすることなしに別プロジェクトで利用できるようにする | npm link | yarn link |
|  |  |  |
