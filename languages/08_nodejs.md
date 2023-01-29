# node.js

## インストール

* [nvmからインストール](https://kazuhira-r.hatenablog.com/entry/2021/03/22/223042)
 * nvmとはNode.jsをインストールしたり、既にインストール済みのNode.jsを最新の安定版にアップデート、バージョンを変更したりするツール
 * EOLは[ここ](https://endoflife.date/nodejs)で確認

```sh
# ubuntuで検証済。事前にgitやcurlをインストールされていることが必要

# nvmリポジトリをクローン
git clone https://github.com/nvm-sh/nvm /opt/nvm/

# 全ユーザーにアクティベートするためにprofileを作成
echo '#!/bin/sh' > /etc/profile.d/nvm.sh && echo -e "\nsource /opt/nvm/nvm.sh" >> /etc/profile.d/nvm.sh
source /etc/profile

# node.jsのインストール
nvm install node   # 最新版インストール
nvm install --lts  # 安定版インストール
nvm install v0.0.0 # バージョン指定インストール

# インストールされているnodeバージョン確認
nvm ls

# nvmで使用nodeバージョンの切替
nvm use v16.15.0

# インストール可能なバージョン一覧
nvm ls-remote

# nodeのアンインストール
nvm uninstall v16.15.0
```


## [リリースサイクル](https://github.com/nodejs/Release)

```javascript
global.__dirname  # 実行中ソースコードのディレクトリパス
global.__filename # 実行中ソースコードのファイルパス¥

# 各種パラメーターの取得
process.env       # 環境変数
process.argv      # 実行時引数
process.cwd()     # カレントディレクトリ
process.platform  # 実行環境

process.exit([code]) # プロセスの終了

process.on("uncaughtException", (err) => {}) # 例外処理
```

* 時間計測

```javascript
console.time("timerName")     # 計測開始
console.timeEnd("timerName")  # 計測終了
console.timeLog("timerName")  # 途中経過

# 例
console.time("timerName")
page.waitForTimeout(10000)
console.timeEnd("timerName")
```

## コマンド

* 基本

```sh
 # インタラクティブシェル
 node
 # バージョン確認
 node -v
```

* デバッグ

```sh
node inspect 実行ファイル名.js

# replと入力するとreplモードになり、user.nameのようなプロパティを確認可能
debug>repl
```

