# javascript

- [javascript](#javascript)
  - [注意点](#注意点)
  - [文法](#文法)
    - [基本](#基本)
      - [繰り返し](#繰り返し)
      - [if文](#if文)
      - [抽出](#抽出)
      - [関数](#関数)
    - [javascript独自](#javascript独自)
      - [document(DOM)](#documentdom)
      - [event](#event)
      - [setTimeout](#settimeout)
      - [IntersectionObserver](#intersectionobserver)
  - [よく使う](#よく使う)
  - [ライブラリ](#ライブラリ)
  - [linter](#linter)

## 注意点

- htmlファイルのscriptタグの中に書く
- scriptタグのにjsファイルを読み込ませる → ```<script src="index.js"></script>```
- head, bodyのどちらかに入れる → bodyの一番下がベストプラクティス

## 文法

- プロパティ
- メソッド

```javascript
let abc = "abc def";

// プロパティ。末尾に()がつかない
console.log(abc.length);
// メソッド。末尾に()がつく
console.log(abc.toUpperCase());
```

### 基本

- 出力

```javascript
console.log("出力したい内容")
console.trace("出力したい内容") // スタックトレースと一緒に出力
```

- 時間計測

```node
console.time("timerName")     // 計測開始
console.timeEnd("timerName")  // 計測終了
console.timeLog("timerName")  // 途中経過

// 例
console.time("timerName")
page.waitForTimeout(10000)
console.timeEnd("timerName")
```

- 変数

```javascript
let height;        // 変数宣言のみ
let height = 170   // 変数
var height = 170   // 変数。古い記法で基本的に非推奨
const height = 170 // 定数
```

- テンプレートリテラル(テンプレート文字列ともいう)
  - 変数と文字列などを組み合わせて出力する内容が書きやすい
    - 変数は`${変数名}`で表示し、\`(バッククォート)で文を囲む

```javascript
const height = "170"
// 通常の書き方
console.log("身長は" + height + "です。")
// テンプレートリテラル
console.log(`身長は${height}です。`)
```

- 型確認

```javascript
typeof(cy.url())
```

- 型変換

```javascript
// string型へ変換
String(値)
値.toString()

// number型へ変換
Number('1234')
// number型へ変換し、小数点を切り捨て。第2引数は10進数の10
parseInt('24.82', 10);

// ロケール（=言語、国、地域、などを表現する、例：ja）に合わせた数値表現を行う。日本語や英語の場合には数値を3桁カンマ区切りにする
(123456).toLocaleString() // '123,456'

// jsonへ変換
JSON.stringify(ターゲット)
// jsonから変換
JSON.parse(ターゲット)
```

- 配列

```javascript
let sample = [1,2,3]

let sample = New Array(1,2,3) // 一般的でない定義方法

sample.push(4)           // 末尾に追加 [1,2,3,4]
sample.unshift(0)        // 先頭に追加 [0,1,2,3,4]
sample.pop()             // 末尾を削除 [0,1,2,3]
sample.shift()           // 先頭を削除 [1,2,3]

let sample1 = [1,2,3]
let sample2 = [4,5,6]
sample1.push(sample2)    // 追加対象も配列として追加 [1,2,3,[4,5,6]]
sample1.push(...sample2) // 追加対象を配列解除して追加 [1,2,3,4,5,6]
```

- オブジェクト型

```javascript
// オブジェクト型の中にオブジェクト型や関数も定義可能
const person = {
  hobby: {
    music: guitar,
    sports: baseball
  },
  getHobby: function() {
    console.log(this.hobby.music + this.hobby.sports)
  }
};

// キーの指定の仕方1
person.hobby.music = 'organ' 
// キーの指定の仕方2。キー名が動的な場合などに使える
const keyName = 'hobby';
person[keyName].music = 'piano' 

// クラス内の関数は__proto__に格納されている
// そのため、下記のように丁寧に書き出して呼出も可能だが一般的には省略
person.__proto__.getHobby()
```

- クラス

```javascript
// 変数化しなくてもOK
let クラス名 = class {
  constructor(値) {
    this.メンバ変数 = 値
    this._メソッド名()
  }

  メソッド名() {
    処理内容
  }
  // プライベートメソッドの暗黙的表示
  _メソッド名() {
    処理内容
  }
};

// クラス初期化
let 変数 = new クラス名(値)
```

- thisとグローバルオブジェクトとbind
  - thisは直近で呼ばれているプロジェクトに依存する
    - class内で呼ばれているのであればclassを参照
    - グローバルオブジェクト内で呼ばれていればグローバルオブジェクトを参照
  - `.bind(指定クラス名(thisもあり))`とすることでそのクラス内のthisを指定クラス名とする

```javascript
// 1 クラス外でthisを呼ぶとグローバルオブジェクトを対象とする
console.log(this)

let sampleClass = class {
  constructor(値) {
    this.メンバ変数 = 値
    this._メソッド名()
  }
  // 2 このthisはsampleClassを参照
  console.log(this)
  
  // setTimeoutはグローバルオブジェクトのためwindowを頭につけても動作する
  // ただ、グローバルオブジェクトの関数は呼出時に省略可能なので慣例的にwindowとつけない
  // 3 下記の場合はwindowのグローバルオブジェクト内でthisが呼ばれている
  window.setTimeout(function abc() {
      console.log(this)
  }, 2000)
  // 4 .bind(this)と付与することで.bind(this)の同階層(setTimeout内ではなくsetTimeout外)と同等のthisを付与することになる
  window.setTimeout(function abc() {
      console.log(this)
  }.bind(this), 2000)
};
```

- クラス継承

```javascript
class 親クラス名 {
  constructor(値) {
    this.メンバ変数 = 値
    this._メソッド名()
  }

  メソッド名1() {
    処理内容
  }
  // プライベートメソッドの暗黙的表示
  _メソッド名() {
    処理内容
  }
}

class extends 親クラス名 {
  constructor(値) {
    super(値);
  }

  メソッド名() {
    // 親クラスのメソッド呼び出しの場合
    super.親クラス名メソッド名
  }
  // 親クラスの同名メソッドを上書きし、再定義(オーバーライド)
  メソッド名1() {
    console.log("オーバーライド")
  }
}
```

#### 繰り返し

- for文

```javascript
// for 基本文
for (let i = 0; i < 10; i++) {
  console.log(i);
}

// 配列を扱う場合
const array = [1, 2, 3, 4, 5]

// for in はキーを出力
for (let i in array) {
  console.log(arr[i]);
}

// for of はバリューを出力
let arr = ["aa", "bb", "cc"]
for (let v of arr) {
  console.log(v);
}
```

- forEach
  - 以下の引数が自動で渡される
    - 第1引数 = 値(バリュー)
    - 第2引数 = 添字(キー)、
    - 第3引数 = 配列

```javascript
const array = [1, 2, 3, 4, 5]

array.forEach(function(value, index, array ) {
    console.log(`バリューを3倍: ${value * 3}`)
    console.log(`キー: ${index}`)
    console.log(`配列: ${array}`)
})

// アロー関数の場合
array.forEach(value => {
    console.log(value * 3)
})
// 引数1つ、処理内容1行ならアロー関数を用いて以下まで省略可能
array.forEach(v => console.log(v * 3))
```

- reduce
  - 第1引数 = 配列で読み込んだ値を繋ぎ合わせた値 ※最初はそのままの値
  - 第2引数 = 配列で読み込んだ値の次の値
  - 第3引数 = 読み込み始める配列の値(オプション)

```javascript
const str = "sample";
const strArry = str.split("");

const result = strArry.reduce((accumulation, current ) => {
  console.log(`accumulation: ${accumulation}`);
  console.log(`current: ${current}`);
  return `${accumulation}_${current}`
})

console.log(`result: ${result}`);
// 以下が出力される
accumulation: s
current: a
accumulation: s_a
current: m
accumulation: s_a_m
current: p
accumulation: s_a_m_p
current: l
accumulation: s_a_m_p_l
current: e
result: s_a_m_p_l_e

// 配列の先頭に組み込みたい場合は第3引数に空文字を指定
const result = strArry.reduce((accumulation, current ) => {
  console.log(`accumulation: ${accumulation}`);
  console.log(`current: ${current}`);
  return `${accumulation}_${current}`
}, "")

// 配列の先頭に組み込みたい場合は第3引数に空文字を指定
accumulation: 
current: s
accumulation: _s
current: a
accumulation: _s_a
current: m
accumulation: _s_a_m
current: p
accumulation: _s_a_m_p
current: l
accumulation: _s_a_m_p_l
current: e
result: _s_a_m_p_l_e
```

- while

```javascript
while (条件) {
  // 処理内容
}
```

#### if文

```javascript
if (num > 80) {
    console.log("numは80より大きいです。");
} else if (num >= 60) {
    console.log("numは60～80の間です。");  
} else {
    console.log("numは60未満です。");
}

// ===(イコール3つ) はデータ型も含めて比較
if (1 === '1') {
    console.log("このパターンはfalse");
// ==(イコール2つ) はデータ型含めず比較
} else if (1 == '1') {
    console.log("このパターンはtrue");  


// 暗黙的型変換
if (text) {
  console.log("OK")
}
```

- switch文

```javascript
let pref = 'Osaka';

switch (pref){
  case 'Tokyo':
    console.log('住所は東京都です');
    break;
  case 'Osaka':
    console.log('住所は大阪府です');
    break;
  default:
    console.log('住所はその他です');
}
```

#### 抽出

- 部分一致

```javascript
if ( str.match('sample') ) {
  console.log("OK")
}
```

- 一部抽出

```javascript
// 最後の文字列を抽出
"ABC31ABC52".slice(-1);

// 最後の文字列を除いて抽出
"ABC31ABC52".slice(0, -1);

// 前後のスペースを消去
"  ABC31  ABC52  ".trim() // 'ABC31  ABC52'
"  ABC31ABC52  ".trim()   // 'ABC31ABC52'
```

- 数字のみを抽出

```javascript
"ABC31ABC52".match(/\d+/g); // ['31', '52']
```

- 置換

```javascript
"ABC31  ABC52".replace(/\s+/, ""); // スペースを消去 'ABC31ABC52'
```

#### 関数

javascriptの特徴として関数を変数で定義できる

```javascript
// この書き方は呼出行より後に書かれても呼出可能。javascriptでは関数の定義を先に確認してから実行文を上から読み込むため
function double ( num ) {
    return num * 2
}

// 上記と同じで違う書き方。関数を変数として定義
// ただし、この書き方は呼出行より後に書かれたら呼出不可
const double = function ( num ) {
    return num * 2
}

// 「無名関数(anonymous function)」 = 下記のように関数名を持たない関数
function ( num ) {
    return num * 2
}

// デフォルト引数を設定する場合
function double ( num = 5 ) {
    return num * 2
}

// 関数呼出
double(4);
```

- コールバック関数

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
  - 関数リテラル(匿名関数,無名関数ともいう)をシンプルに記述する手法をアロー関数という
  - アロー関数は引数の丸かっこと処理内容の波かっこの間に`=>`の記述が必須
  - 以下の条件で省略できる部分がある
    - 条件なし
      - `function`の省略
    - 処理内容が1行
      - 処理内容の波かっこ`{}`の省略
      - `return`の省略
    - 引数が1つ & デフォルト引数なし
      - 引数の丸かっこ`()`の省略

```javascript
// 変更元となる基本アロー関数。functionを省略、引数の丸かっこ()と処理内容の波かっこ{}の間にアロー=>を配置
let getTriangle = ( base, height ) => {
  return base * height / 2;
};
console.log('三角形の面積は' + getTriangle(10,2)); //三角形の面積は10

// ↑は下記の関数リテラルと同様
let getTriangle = function ( base, height ) {
 return base * height / 2;
};
console.log('三角形の面積は' + getTriangle(10,2));//三角形の面積は10

// 処理内容が1行の場合、波かっこ{}省略記述が可能
let getTriangle = ( base, height ) => return base * height / 2;
console.log(getTriangle(10,2)); //10

// 処理内容が1行の場合、さらにreturn省略記述が可能
let getTriangle = ( base, height ) => base * height / 2;
console.log(getTriangle(10,2)); //10

// 引数が一つの場合、引数の丸かっこ()省略記述が可能
let getCircle = radius => radius * radius * Math.PI;
console.log(getCircle(10))//314.1592653589793;

// 引数がない場合はカッコを省略せずに記述
let show = () => console.log('Hello, world!'); 
show(); // Hello, world!
```

- 通常関数とアロー関数の比較表。通常関数の記述をアロー関数で記述した場合に省略できる箇所は<font color="red">赤文字</font>で表記

<table>
  <tr>
    <th>通常関数</th>
    <th>アロー関数</th>
    <th>補足</th>
  </tr>
  <tr>
    <td><code><pre>
const double = <font color="red">function</font> (num) {
    return num * 2
}
    </code></td>
    <td><code><pre>
const double = (num) => {
    return num * 2
}
    </code></td>
    <td>function省略<br>条件=なし</td>
  </tr>
  <tr>
    <td><code><pre>
const double = <font color="red">function</font> (num) <font color="red">{</font>
    return num * 2
<font color="red">}</font>
    </code></td>
    <td><code><pre>
const double = (num) => return num * 2
    </code></td>
    <td>function省略<br>波かっこ{}省略<br>条件=処理内容1行</td>
  </tr>
  <tr>
    <td><code><pre>
const double = <font color="red">function</font> <font color="red">(</font>num<font color="red">) {</font>
    return num * 2
<font color="red">}</font>
    </code></td>
    <td><code><pre>
const double = num => return num * 2
    </code></td>
    <td>function省略<br>波かっこ{}省略<br>丸かっこ()省略<br>条件=デフォルト引数なし&処理内容1行</td>
  </tr>
  <tr>
    <td><code><pre>
const double = <font color="red">function</font> <font color="red">(</font>num<font color="red">) {</font>
    <font color="red">return</font> num * 2
<font color="red">}</font>
    </code></td>
    <td><code><pre>
const double = num => num * 2
    </code></td>
    <td>function省略<br>波かっこ{}省略<br>丸かっこ()省略<br>return省略<br>条件=デフォルト引数なし&処理内容1行</td>
  </tr>
  <tr>
    <td><code><pre>
array.forEach(<font color="red">function(</font>value<font color="red">) {</font>
    console.log(value * 3);
<font color="red">}</font>)
    </code></td>
    <td><code><pre>
array.forEach( value => console.log(value * 3) );
    </code></td>
    <td>forEachも同様</td>
  </tr>
</table>

### javascript独自

#### document(DOM)

- Document Object Model
- ブラウザのデータを取得
- DOMで取得した要素をノードという

```javascript
// 指定idのhtmlを取得
document.querySelector('#title')

// 指定idのhtmlを取得。getElementByIdは古い記法
document.getElementById("input").value

// 指定classのhtmlを取得
document.querySelector('.sub-title')

// 該当タグで最初の値のみを取得
document.querySelector("タグ")

// 該当タグの全てを取得
document.querySelectorAll("タグ")

// bodyの子要素を取得
document.body.children

// 指定タグの内容を取得
const sample = document.querySelector('.sub-title')
sample.innerHTML    // htmlタグ含めて表示
sample.textContent  // htmlタグ含めず表示
sample.style.color  // cssスタイルを表示

// 指定タグの内容を変更
sample.classList.add('underline')    // classにunderlineを追加
sample.classList.remove('underline') // classからunderline消去
sample.classList.toggle('underline') // classからunderlineの有無(true/false)を切り替える

// 指定タグのプロパティ一覧表示
document.querySelector('タグ').attributes
// 指定タグの中に指定プロパティがあるかを確認
document.querySelector('タグ').hasAttribute('loop')
```

- DOMContentLoadedとloaded

```javascript
// DOMContentLoaded 画像や映像などのダウンロードを待たず、DOMツリーの読込が完了したら発火する
// documentでもwindowでも登録可能
document.addEventListener("DOMContentLoaded", callback)
window.addEventListener("DOMContentLoaded", callback)

// load 画像や映像などのダウンロードを待つ
// windowでのみ登録可能
window.addEventListener("load", callback)
```

#### event

- ブラウザ上でホバーする、クリックする、テキスト入力するなどの全ての行為が認識されており、それらを常時監視しているものを`イベントリスナー`という

```javascript
// addEventListener(イベント名, 関数)
form.addEventListener(submit, 
function () {
    console.log("test")
})


const sample = document.querySelector('#btn')

// addEventListenerの基本形式
sample.addEventListener('イベント名', callback)
// クリック時のイベントを登録
sample.addEventListener('click', callback)
// イベント消去
sample.removeEventListener('click', callback)

// イベントハンドラでクリック時のイベントを登録。addEventListener('click', callback)と同様だが1つしかイベントを登録できないので可能な限りaddEventListenerを使用
sample.onclick = (callback)

event.preventDefault() // デフォルトの動作を実行しない
```

#### setTimeout

```javascript
setTimeout(function abc() {
    console.log("処理内容")
}, 2000)       // 秒数を指定する 2000ミリ秒なので2秒

setTimeout(() => {
    console.log("処理内容")
}, 2000)

// 一定時間毎に処理を実行
let intervalId = setInterval(callback, milliseconds);
```

- localStorage → ブラウザに永続的に保存

```javascript
// データの保存
localStorage.setItem('キー','値')

// データの取得
localStorage.getItem('キー','値')
```

#### IntersectionObserver

- 交差を監視するというのが直訳の意味
  - マウススクロールの動きを検知する

```javascript
// 対象を定義
const target = document.querySelector('.target')
// IntersectionObserverで使用するcallback関数を定義
const callback = fucntion() {
  alert('test')
}
// 初期化
const io = new IntersectionObserver(callback)
// 監視開始。対象を検知したらアラートが出る
io.observe(target)
```

```javascript
// 対象を定義
const target = document.querySelector('.target')
const callback = fucntion(entries, observer) {
  entries.forEach(entry => {
    // 対象を検知したら
    if(entry.isIntersecting) {
      console.log('対象検知内')
      // 対象を監視対象から外す
      observer.unobserve(entries.target)
    } else {
    console.log('対象検知外')
  });
}
// IntersectionObserverのオプション定義
const options = {
  root: null,                      // そんなに使わない
  rootMargin: "300px 0px 0px 0px", // 検知する範囲のマージン 上右下左の順
  threshold: 1                     // 対象を検知する境界線のことで0が対象の端に触れ始め、1が触れ終わり
}
// 初期化
const io = new IntersectionObserver(callback, options)
// 監視開始
io.observe(target)
```

```javascript
```

```javascript
```

## よく使う

- `YYYYMMDD-hhmm`形式で時刻を取得する

```javascript
// 現在の日付と時刻を取得
const jstDiffTime = 9 * 60 * 60 * 1000;                        // JSTはUTC+9なので、9時間をミリ秒で表現
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

## ライブラリ

- GreenSock
  - [gsap(**g**reen **s**ock **a**nimation **p**latform)](https://gsap.com/docs/v3/)
    - version=2はTweenMaxと呼ばれる
    - version=3がgsapと呼ばれる

## linter

- インストール

```sh
npm install --save-dev eslint
```

- 解析

```sh
eslint 対象ファイル
```
