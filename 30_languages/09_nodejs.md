# node.js

- [node.js](#nodejs)
  - [公式情報](#公式情報)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
    - [デバッグ](#デバッグ)
  - [構文](#構文)
    - [モジュールシステム](#モジュールシステム)
    - [モジュール](#モジュール)
      - [EventEmitter](#eventemitter)
      - [path](#path)
      - [async function](#async-function)
      - [fs](#fs)
      - [エンコード](#エンコード)
      - [URL](#url)
      - [HTTP](#http)
      - [マルチスレッド](#マルチスレッド)
    - [コード例](#コード例)

## 公式情報

- [リリースサイクル](https://github.com/nodejs/Release)
- [チェンジログ](https://github.com/nodejs/node/tree/main/doc/changelogs)
  - nodeアップグレードの際にどういう変更があるか要確認

## インストール

- [nvmからインストール](https://kazuhira-r.hatenablog.com/entry/2021/03/22/223042)
  - nvmとはNode.jsをインストールしたり、既にインストール済みのNode.jsを最新の安定版にアップデート、バージョンを変更したりするツール
  - EOLは[ここ](https://endoflife.date/nodejs)で確認

```sh
# ubuntuで検証済。事前にgitやcurlをインストールされていることが必要

# nvmリポジトリをクローン
git clone https://github.com/nvm-sh/nvm /opt/nvm/
# またはインストーラーからインストール
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash

# 全ユーザーにアクティベートするためにprofileを作成
echo '#!/bin/sh' > /etc/profile.d/nvm.sh && echo -e "\nsource /opt/nvm/nvm.sh" >> /etc/profile.d/nvm.sh
source /etc/profile

# node.jsのインストール
nvm install node   # 最新版インストール
nvm install --lts  # 安定版インストール
nvm install v0.0.0 # バージョン指定インストール

## バージョンアップグレード時に使用可能なオプション
### 旧バージョンで使用していたグルーバルパッケージを新バージョンでもインストールする
nvm install --reinstall-packages-from=18.20.3(旧バージョン) 20.14.0(新バージョン)

# インストールされているnodeバージョン確認
nvm ls

# nvmで使用nodeバージョンの切替
nvm use v16.15.0

# インストール可能なバージョン一覧
nvm ls-remote

# エイリアス
## nvm alias エイリアス名 バージョン名 でエイリアスを作成し、バージョンを割り当てる
nvm alias default 16.15.0

# nodeのアンインストール
nvm uninstall v16.15.0

# nvmのバージョン確認
nvm current
```

## コマンド

- 基本

```sh
# インタラクティブシェル
node
# バージョン確認
node -v

# node.jsが使用するV8ヒープ領域の最大サイズ（メモリ上限）を指定するオプション
# デフォルト値: 約 1.5GB のメモリ（32ビット環境では約512MB）
# メモリ上限値を変更することで、より大きなメモリを使用可能にする
NODE_OPTIONS="--max-old-space-size=4096 yarn install
```

### デバッグ

- repl

```sh
node inspect 実行ファイル名.js

# replと入力するとreplモードになり、user.nameのようなプロパティを確認可能
debug>repl

# helpで以下のような使い方表示
# .exitで終了

repl                  # 別画面で変数名の中身確認可能。Ctl + c で終了

run, restart, r       # デバッグ起動
kill                  # デバッグ終了

cont, c               # コンティニュー
next, n               # ステップネクスト
step, s               # ステップイン
out, o                # ステップアウト
backtrace, bt         # バックトレース
list                  # list(5) で5行目付近を表示
setBreakpoint, sb     # sb(4) で４行目にブレイクポイント設置
clearBreakpoint, cb   # cb("ファイル名", 4) でブレイクポイント消去
breakpoints           # ブレイクポイント表示
watch(expr)           # watch(変数名)で watchersで表示できるように記憶
watchers              # watch(変数名)で記憶した変数を一覧表示
```

```sh
# DevToolsを使ったデバッグ方法はブラウザと連携
# chromeで chrome://inspect とURLを入力
# chrome検証ツールが開くので「Open dedicated DevTools for Node」を開く
node inspect 実行ファイル名.js
```

- debugger

```node
// node inspect 実行ファイル名.js で c(ont) 実行時に一気にdebuggerまでジャンプ可能
console.log(myURL.port)
debugger;
console.log(myURL.host)
```

- console.Console
  - ログファイルを作成

```sample.js
const fs = require('fs');
const log = fs.createWriteStream('result.log');
const logger = new console.Console(log);

let user = {
  name: 'Ichiro',
  age: 20,
  area: 'Okinawa'
}
// result.logへ各要素を書込
logger.log(user);
logger.log(user.name);
logger.log(user.age);
```

```result.log
{ name: 'Ichiro', age: 20, area: 'Okinawa' }
Ichiro
20
```

## 構文

- console.log置換

```node
console.log("message: %s", "文字列")
console.log("message: %d", 1234) //整数、または少数
console.log("message: %i", 1234) //整数
console.log("message: %f", 1234) //少数
console.log("message: %o", {obj: "OBJECT"})
```

- パス確認

```node
global.__dirname  // 実行中ソースコードのディレクトリパス
global.__filename // 実行中ソースコードのファイルパス
```

- 各種パラメーターの取得

```node
process.env       // 環境変数
process.argv      // 実行時引数
process.cwd()     // カレントディレクトリ
process.platform  // 実行環境

process.exit([code]) # プロセスの終了

process.on("uncaughtException", (err) => {}) // 例外処理
```

- json

```node
// json形式で出力
JSON.stringify(オブジェクト)
JSON.stringify(オブジェクト, null, 2) // 整形
```

- function(古い記法)を使用する場合

<table>
  <tr>
    <th>項目</th>
    <th>class</th>
    <th>function</th>
  </tr>
  <tr>
    <td>クラス定義</td>
    <td><code><pre>
    let クラス名 = class {
      constructor(値) {
        this.メンバ変数 = 値
      }
      // 処理内容
    }
    </pre></code></td>
    <td><code><pre>
    let クラス名 = function (値) {
      this.メンバ変数 = 値
    }
    </pre></code></td>
  </tr>
  <tr>
    <td>メソッド定義</td>
    <td><code><pre>
    メソッド名() {
      // 処理内容
    }
    </pre></code></td>
    <td><code><pre>
    クラス名.prototype.メソッド名 = function () {
      // 処理内容
    }
    </pre></code></td>
  </tr>
  <tr>
    <td>クラス継承</td>
    <td><code><pre>
    let 子クラス名 = class extends 親クラス名 {
      constructor(値) {
        super(値);
      }
      // 処理内容
    }
    </pre></code></td>
    <td><code><pre>
    let util = require("util");
    let 子クラス名 = function (値) {
      // 親クラス呼出時にはthisを書換が必要
      子クラス名.super_.call(this, value);
    };
    util.inherits(子クラス名, 親クラス名)
    子クラス名.prototype.メソッド名 = function () {
      // 親クラスメソッド呼出。この時もthisを書換が必要
      子クラス名.super_.prototype.親クラスメソッド名.call(this)
    }
    </pre></code></td>
  </tr>
</table>

### モジュールシステム

モジュールシステムとはコードの構造を分割し、モジュールとして機能を再利用可能にする仕組み
importとexportを使用して、モジュール内の機能や変数を他のファイルに渡したり、他のファイルから取得したりしする

- `CommonJS`:(CJS)
  - `.js`拡張子
  - node.jsはデフォルトでCJSをモジュールとして扱う
  - require() 関数を使用してモジュールを読み込み、module.exports を使用してエクスポートする
  - 主にサーバーサイドでの使用に特化して設計された
- `ECMAScript Modules`:(ESM)
  - `.mjs`(**m**odule **j**ava**s**criptファイル)拡張子
  - node.js では、.mjs ファイルは として扱われます。
  - import文を使用してモジュールを読み込み、関数を使用してモジュールを読み込み、exportを使用してエクスポートする
  - `.mjs`に変更することでnode.jsにそのファイルがESM形式であると明示的に伝えることができる
  - package.jsonに`"type": "module"`を指定して使用
  - Webブラウザとnode.jsの両方でサポートされている

### モジュール

- モジュール使用

```node
let 変数 = require("モジュールのパス")
// module.jsというファイルで定義されていたら require("module") となる
// 実行ファイルパスからの相対パスで指定
```

- モジュール読込順序
  - 以下の優先順位で呼び出される(require("sample")と仮定)
    - コアモジュール(拡張子はなくてもOK)
    - ファイル
      - sample
      - sample.js
      - sample.json
      - sample.node
    - ディレクトリ
      - sample/package.jsonにあるmain
      - sample/index.js
      - sample/index.json
      - sample/index.node
    - node_modules
- モジュール呼出の特徴
  - 一度呼ばれたファイルはキャッシュされる
    - これは同じファイルが複数回呼び出されても(ファイル内ローカルにある)初期化は1回しかされないと言い換えられる

- モジュール作成

```node
// 基本形
module.exports = モジュール名

// オブジェクトモジュール化
module.exports = {
  foo: "let",
  num: 111,
  method: function () { console.log(abc"); }
}

// 関数モジュール化
let 関数1 = function () { };
let 関数2 = function () { };
module.exports = {
  関数1,
  関数2
}

// クラスモジュール化
let クラス名1 = class () { };
let クラス名2 = class () { };
module.exports = {
  クラス名1,
  クラス名2
}
```

#### EventEmitter

nodejsのイベントに関するコア機能

```node
// イベント発火時の処理を設定
EventEmitter.on(name, lister)
EventEmitter.once(name, lister) // 一度だけの実行

// イベント発火時の処理を削除
EventEmitter.off(name, lister)

// イベント発火
EventEmitter.emit(name, argv)
```

- 注意事項
  - 発火済イベントはoffで止めることは不可
  - emit()で呼びされる処理はon()での処理の順に同期的に呼び出される

```node
let EventEmitter = require("events");
let event = new EventEmitter();

let call = function () {
  console.log("'event' is called.");
  event.off("event", call);
};

event.on("event", call);

event.emit("event");
event.emit("event"); // 2回目は呼び出されない
```

#### path

```node
let path = require("path")

// ディレクトリ名の取得
path.dirname(パス)

// ファイル名の取得
path.basename(パス)

// 拡張子名の取得
path.extname(パス)

// パス文字列結合
path.join(パス1, パス2)

// パス文字列の正規化(/var/www/../log -> /var/log と綺麗に表示してくれる)
path.normalize(パス)

// パス表示スタイルを指摘
path.posix.basename(パス) // POSIXスタイル
path.win32.basename(パス) // Windowsスタイル
```

#### async function

```node
(async function () {
// この中にこの中に処理しない内容をawait付きで実装
})();
```

#### fs

<font color="red">**F**</font>ile<font color="red">**S**</font>ystem(ファイルシステムの頭文字からfsと呼称

- 同期処理

おすすめではない

```node
let path = require("fs");
let data = fs.readFileSync(読取ファイルパス, "utf8");
fs.writeFileSync(書込ファイルパス, data, "utf8");
```

- 非同期処理(関数)

ファイルサイズが小さく後続処理も簡易な場合に利用

- 読込処理
  - `fs.readFile(1:読取ファイルパス, 2:読込オプション(エンコード), 3:コールバック関数(err, data);`

```node
let fs = require("fs");
let path = require("path");

fs.readFile(path.join(__dirname, "読込ファイル名"), "utf8", (err, data) => {
  if (err) {
    console.log(err.message);
    return;
  }
  console.log(data);
});
```

- 書込処理
  - `fs.writeFile(1:書込ファイルパス, 2:書込データ、3:書込オプション(エンコード), 4:コールバック関数(err);`

```node
let fs = require("fs");
let path = require("path");

fs.readFile(path.join(__dirname, "読込ファイル名"), "utf8", (err, data) => {
  if (err) {
    console.log(err.message);
    return;
  }
  fs.writeFile(path.join(__dirname, "書込ファイル名"), data, "utf8", (err) => {
  if (err) {
    console.log(err.message);
    return;
  }
  console.log("Writing completed!");
  });
});
```

- async/await で非同期実装

```node
let fs = require("fs");
let path = require("path");
let util = require("util");

let readFile = util.promisify(fs.readFile);
let writeFile = util.promisify(fs.writeFile);

(async function () {
  try {
    let data = await readFile(path.join(__dirname, "読込ファイル名"), "utf8");
    await writeFile(path.join(__dirname, "書込ファイル名"), data, "utf8");
    console.log("Writing completed!");
  } catch (err) {
    console.log(err.message);
  }
})();
```

- 非同期処理(ストリーム)

ファイルサイズが大きく後続処理が複雑な場合に利用

- 読込処理
  - `fs.createReadStream(1:書込ファイルパス, 2:書込オプション(エンコード));`

```node
let fs = require("fs");
let path = require("path");
let data = "";

let reader = fs.createReadStream(path.join(__dirname, "書込ファイル名"), "utf8");
reader.on("data", (chunk) => {
  data += chunk;
})
reader.on("end", () => {
  console.log(data);
});
reader.resume(); //再開
```

- 書込処理
  - `fs.createWriteStream(1:書込ファイルパス, 2:書込オプション(エンコード));`

```node
let fs = require("fs");

let reader = fs.createReadStream(読取ファイルパス, "utf8");
let writer = fs.createWriteStream(書込ファイルパス, "utf8");
reader.pipe(writer);
reader.resume();
```

#### エンコード

標準では日本語対応したShift_JISなどがないので、`iconv-lite`を使用するのがおすすめ

- 関数を使ったエンコード/デコード

```node
const iconv = require("iconv-lite");

<バッファ> = iconv.encode(<文字列>, "shiftjis");
<文字列> = iconv.decode(<バッファ>, "shiftjis");
```

- ストリームを使ったエンコード/デコード

```node
const iconv = require("iconv-lite");

iconv.encodeStream("shiftjis");
iconv.decodeStream("shiftjis");
```

#### URL

WHATWG URLの構造

```sh
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                                            href                                             │
├──────────┬──┬─────────────────────┬─────────────────────┬───────────────────────────┬───────┤
│ protocol │  │        auth         │        host         │           path            │ hash  │
│          │  │                     ├──────────────┬──────┼──────────┬────────────────┤       │
│          │  │                     │   hostname   │ port │ pathname │     search     │       │
│          │  │                     │              │      │          ├─┬──────────────┤       │
│          │  │                     │              │      │          │ │    query     │       │
"  https:   //    user   :   pass   @ sub.host.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          │  │          │          │   hostname   │ port │          │                │       │
│          │  │          │          ├──────────────┴──────┤          │                │       │
│ protocol │  │ username │ password │        host         │          │                │       │
├──────────┴──┼──────────┴──────────┼─────────────────────┤          │                │       │
│   origin    │                     │       origin        │ pathname │     search     │ hash  │
├─────────────┴─────────────────────┴─────────────────────┴──────────┴────────────────┴───────┤
│                                            href                                             │
└─────────────────────────────────────────────────────────────────────────────────────────────┘
```

```node
const url = require('node:url');

const myURL = new URL('https://news.google.com/topics/CAAqJggKIiBDQkFTRWdvSUwyMHZNRFZxYUdjU0FtVnVHZ0pWVXlnQVAB?hl=en-US&gl=US&ceid=US%3Aen');
console.log(myURL.protocol)
console.log(myURL.host)
console.log(myURL.search)
console.log(myURL.port)
```

#### [HTTP](https://nodejs.org/docs/latest/api/http.html#httpcreateserveroptions-requestlistener)

- httpサーバー作成

```node
let http = require("http");
let server = http.createServer((request, response) => {
  response.end("Hello World");
});
server.listen(3000);
```

- GETリクエスト(クライアント)

```node
let http = require("http");
let request = http.request(
  "http://localhost:3000/",
  { method: "GET" },
  (response) => {
    response.pipe(process.stdout);
  });
console.log(request);
request.end();
```

- POSTリクエスト(クライアント)

```node
let http = require("http");
let data = "Hello World !";
let url = "http://localhost:3000/";
let options = {
  method: "POST",
  headers: {
    "Content-Type": "text/plain",
    "Content-Length": Buffer.byteLength(data)
  }
};
let request = http.request(url, options, (response) => {
  response.pipe(process.stdout);
});
request.on("error", (err) => {
  console.log(err.message)
});
console.log(request);
request.end(data);
```

#### マルチスレッド

- 親プロセス（parent.js）

```node
let { fork } = require("child_process");
let path = require("path");

// fork(ファイルパス, 実行時引数, オプション)の形式でファイルを非同期読込
let subprocess = fork(path.join(__dirname, "child.js"), { execArgv: [] });
subprocess.on("message", (msg) => {
  console.log(`PARENT received message: [${JSON.stringify(msg)}]`);
});
// subprocess.onで子プロセスからメッセージ受信
subprocess.on("close", (code) => {
  console.log(`Child process exited with code [${code}]`);
});
subprocess.send({ hello: "child from parent." }); // 子プロセスへ通信
```

- 子プロセス（child.js）

```node
// process.onで親プロセスからメッセージ受信
process.on("message", (msg) => {
  console.log(`CHILD received message: [${JSON.stringify(msg)}]`);
});
 
setTimeout(() => {
  process.send({ hello: "parent from child." }); //process.sendは親プロセスへ通信
  process.exit(); // 意図せず終了しない事を防ぐため
}, 3000);
```

- マルチスレッド処理を実装する際の注意点
  - 小さく軽い処理はマルチスレッド化しない
  - ファイルI/Oをマルチスレッド化しない
- プロセス間通信を行う方法
  - `.send( <object> )` でメッセージ送信、
  - `.on("message", (<object>) => { })` でメッセージ受信

### コード例

- グローバルIP確認

```node
var assert = require('http');
http.get('https://httpbin.org/ip',
  function (err, response, body) {
    if (err) {
      console.error('Error:', err);
      return;
    }
    console.log('Global IP:', response.body);
  }
);
```
