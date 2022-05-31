# node.js

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

```sh
 # インタラクティブシェル
 node
 # バージョン確認
 node -v
```
