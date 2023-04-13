# javascript

- [javascript](#javascript)
  - [文法](#文法)
    - [基本](#基本)
    - [javascript独自](#javascript独自)
  - [よく使う](#よく使う)
  - [注意点](#注意点)
  - [linter](#linter)

## 文法

### 基本

- 出力

```javascript
console.log("出力したい内容")
console.trace("出力したい内容") # スタックトレースと一緒に出力
```

- 変数

```javascript
let height;        # 変数宣言のみ
let height = 169   # 変数
var height = 169   # 変数。古い記法で基本的に使うべきでない
const height = 169 # 定数
```

- 関数

```javascript
function double(num) {
    return num * 2
}
```

コールバック関数とは?

ある関数を呼び出す時に、引数に指定する別の関数のこと。以下例でgreetingがコールバック関数となる

```javascript
function greeting(name) {
  alert(`Hello, ${name}`);
}

function processUserInput(callback) {
  const name = prompt("Please enter your name.");
  callback(name);
}

processUserInput(greeting);
```

- アロー関数

```javascript
# 関数リテラル(匿名関数,無名関数ともいう)をシンプルに記述する手法をアロー関数という
let getTriangle = (base, height) => {
  return base * height / 2;
};
console.log('三角形の面積は' + getTriangle(10,2)); //三角形の面積は10

 # ↑は下記の関数リテラルと同様
let getTriangle = function(base,height){
 return base * height / 2;
};
console.log('三角形の面積は' + getTriangle(10,2));//三角形の面積は10

# 本文が一文の場合、さらに省略記述が可能
let getTriangle = (base, height) => base * height / 2;
console.log(getTriangle(10,2));//10

# 引数が一つの場合、以下の省略記述が可能
let getCircle = radius => radius * radius * Math.PI;
console.log(getCircle(10))//314.1592653589793;

# 引数がない場合はカッコを省略せずに記述
let show =()=> console.log('Hello, world!'); 
show(); //Hello, world!
```

- 繰り返し

```javascript
const array = [1, 2, 3]
array.forEach(value => {
    console.log(value * 3)
})

for (let i = 0; i < 10; i++) {
  console.log(i);
}

let arr = ["aa", "bb", "cc"]
for (let i of arr) {
  console.log(i);
}
```

- if文

```javascript
if (num > 80) {
    console.log("numは80より大きいです。");
} else if (num >= 60) {
    console.log("numは60～80の間です。");  
} else {
    console.log("numは60未満です。");
}

# 暗黙的型変換
if (text) {
    console.log("OK")
}
```

- 配列

```javascript
let todos = [1,2,3]
todos.push(4)          # 追加 [1,2,3,4]

let todos1 = [1,2,3]
let todos2 = [4,5,6]
todos1.push(todos2)    # 追加対象も配列として追加 [1,2,3,[4,5,6]]
todos1.push(...todos2) # 追加対象を配列解除して追加 [1,2,3,4,5,6]
```

- 型確認

```javascript
typeof(cy.url())
```

- 型変換

```javascript
# string型へ変換
String(値)
値.toString()

# jsonへ変換
JSON.stringify(ターゲット)
# jsonから変換
JSON.parse(ターゲット)
```

- 部分一致

```javascript
if ( str.match('sample') ) {
}
```

### javascript独自

- document → ブラウザのデータを取得

```javascript
# idクラスの値を取得
document.getElementById("input").vaule

# 該当タグの全てを取得
document.querySelectorAll("タグ")
```

- addEventListner(イベント名, 関数)

```javascript
form.addEventListener(submit, 
function () {
    console.log("test")
})
```

- event

```javascript
event.preventDefault() # デフォルトの動作を実行しない
```

- setTimeout

```javascript
setTimeout(function abc() {
    console.log("処理内容")
}, 2000)       # 秒数を指定する 2000ミリ秒なので2秒

setTimeout(() => {
    console.log("処理内容")
}, 2000)
```

- localStorage → ブラウザに永続的に保存

```javascript
# データの保存
localStorage.setItem('キー','値')

# データの取得
localStorage.getItem('キー','値')
```

## よく使う

- `YYYYMMDD-hhmm`形式で時刻を取得する

```javascript
// 現在の日付と時刻を取得
const jstDiffTime = 9 * 60 * 60 * 1000;   // JSTはUTC+9なので、9時間をミリ秒で表現
const date = new Date();
const jstDate = new Date(date.getTime() + jstDiffTime);
const year = jstDate.getFullYear();
const month = String(jstDate.getMonth() + 1).padStart(2, '0'); // 月を2桁にする
const day = String(jstDate.getDate()).padStart(2, '0');        // 日を2桁にする
const hour = String(jstDate.getHours()).padStart(2, '0');      // 時を2桁にする
const minute = String(jstDate.getMinutes()).padStart(2, '0');  // 分を2桁にする

// ファイル名を作成
const filename = `result_${year}${month}${day}-${hour}${minute}.html`;
```

- [javascriptで日付を表示したい場合1](https://web.plus-idea.net/on/javascript-date-string-convert/)
- [javascriptで日付を表示したい場合2](https://qiita.com/toshimin/items/5f13c3b4c28825219231)

## 注意点

- htmlファイルのscriptタグの中に書く
- scriptタグのにjsファイルを読み込ませる → ```<script src="index.js"></script>```
- head, bodyのどちらかに入れる → bodyの一番下がベストプラクティス

## linter

- インストール

```sh
npm install --save-dev eslint
```

- 解析

```sh

eslint 対象ファイル
```
