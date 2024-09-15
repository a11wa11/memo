# typescript

- [typescript](#typescript)
  - [コマンド](#コマンド)
    - [コンパイル](#コンパイル)
  - [構文](#構文)

## コマンド

### コンパイル

`tsc` -> typescriptファイルをjavascriptにコンパイルするためのコンパイラコマンド

```sh
# 基本コンパイル
tsc
# 指定ファイルのみコンパイル
tsc 対象ファイル.ts

# watchモードでリアルタイムコンパイル
tsc 対象ファイル.ts --watch

# 出力ディレクトリを指定してコンパイル
tsc --outDir dist
```

- 注意点
  - `tsc --outDir ディレクトリ名`でコンパイル指定しなければデフォルトでは、元あるtsファイルの場所にそのままコンパイルファイル(js)が作成される

- `.d.ts` -> typescriptの型定義ファイル。javascriptで記述されたコードやライブラリをtypescriptで使用するときに、型の補完と型チェックを提供する
  - 型情報の宣言
    - `.d.ts`ファイルは型情報のみを宣言し、実際のコード（ロジック）は含まない。関数やクラス、変数、モジュールなどがどのような型を持つかを定義
  - javascriptライブラリの型サポート：
    - javascriptで書かれたライブラリ（例 jQueryやLodash）をtypescriptで使う場合、型情報がないとtypescriptの型チェックや型補完が機能しない。そこで、`.d.ts`ファイルを使って、そのライブラリに対して型を定義し、typescriptのコードで型安全性を確保できる
  - コンパイル対象外
    - `.d.ts`ファイルは型定義だけなので、typescriptのコンパイラで .ts ファイルをjavascriptに変換するときに、.d.ts ファイルはコンパイルされません。あくまで開発時の補助として機能する
  - 型チェックの補完
    - `.d.ts`ファイルがあれば、typescriptのエディタやコンパイラは型チェックを行い、エラーの検出やコード補完をサポートする

- `tsconfig.json` -> デフォルトではtsconfig.jsonファイルがプロジェクト内にある場合、その設定に従ってtypescriptファイルをコンパイルする

```tsconfig.json
{
  "include": ["./src/**/*.ts"],
  "exclude": ["node_modules"],
  "compilerOptions": {
    "target": "es6",                          // typescriptコードをどのバージョンのjavascriptに変換するかを指定。ES6(ECMAScript 2015)を出力ターゲットとする
    "module": "commonjs",                     // CommonJS形式のモジュール
    "lib": [
      "es6",
      "es2018",
      "esnext.asynciterable"
    ],
    "rootDir": "src",
    "outDir": "./dist",                        // コンパイル後のファイルを./distディレクトリに出力
    "typeRoots": ["node_modules/@types"],
    "moduleResolution": "node",                // typescriptがモジュールをどのように解決(探索)するかを指定。主に"node"と"classic"の2つのオプションがある。"node"はnode.jsのモジュール解決アルゴリズムを模倣し、"classic"はtypescriptの古い解決方法を使用する
    "baseUrl": ".",

    /* Code Quality checks */
    "noUnusedLocals": true,                    // 使用されていないローカル変数がある場合にエラー
    "noUnusedParameters": true,                // 使用されていない関数のパラメータがある場合にエラー
    "noFallthroughCasesInSwitch": true,        // breakを忘れた場合に警告
    "removeComments": true,                    // コンパイル後のファイルからコメントを削除
    "forceConsistentCasingInFileNames": true,  // ファイル名の大文字小文字を区別する

    /* Strict Type Checks */
    "noImplicitAny": true,                     // 型が明示されていない変数やパラメータが暗黙的にany型になることを禁止
    "noImplicitThis": true,                    // thisの型が暗黙的にanyになることを禁止
    "noImplicitReturns": true,                 // 必ず戻り値を返すことが求められる
    "strictNullChecks": true,                  // null,undefinedを厳密にチェック
    "strictFunctionTypes": true,               // 関数の型互換性をより厳密にチェック

    /* Module Resolution Options */
    "emitDecoratorMetadata": true,             // デコレーターが使用されているクラスに対して、メタデータを生成
    "experimentalDecorators": true,            // TypeScriptの実験的なデコレーター機能を有効にする

    /* Advanced Options */
    "skipLibCheck": true,                      // ライブラリの型チェックをスキップ
    "composite": false,                        // 他のTypeScriptプロジェクトと一緒にビルドされない
    "allowSyntheticDefaultImports": false,     // デフォルトインポートは許可しない
  },
}
```

## 構文

- object

```typescript
let person: {name: string, age: number} = {
    name: "suzuki",
    age: 20
}

let person: {} = {  // オブジェクト型だがそこまで使わず
    name: "suzuki",
    age: 20
}
let person: object = {  // 同じくオブジェクト型だが使わず
    name: "suzuki",
    age: 20
}
```

- 配列

```typescript
# 初期化
let nums: Array<number> = []
let nums: number[] = []
# 要素数を指定
let nums: Array<number> = new Array(3)

# 初期化済配列作成
let family: string[] = ["father", "mother", "brother", "sister"]

# 追加
family.push("grandfather")
```

- enum(列挙型)。限られた選択肢として宣言したい時

```typescript
# enumのKEYは慣例的に全て大文字
enum Size {
    SMALL = 100,
    MEDIUM = 200,
    LARGE = 300
}
```

- tuple

```typescript
// 配列と違い型注釈で[]の中に記載
let pc: [string, number, boolean] = ["test", 11, true]
```

- any どの型を宣言してもいい。別の型の変数に代入も可能

```typescript
let anything: any = ["test", 11, true]
```

- unknown どの型を宣言してもいいが、さらにunknown型変数を代入するには代入先の方注釈に適合していなければならない

```typescript
let unknownInput: unknown = true
let text: string = "text"
unknownInput = "test" 
unknownInput = 33
text = unknownInput // エラーになる
```

- union型 → 複数の型を宣言可能

```typescript
let union: number | string = 11
union = "test"

# 配列の場合
let unions: (number | string)[] = [11, "test"]
```

- Literal型 指定した値のみしか入らない

```typescript
const banana: "banana" = "banana"
const banana = "banana" //constで宣言したらstring型ではなく、Literal型になる
let fruit: "apple" | "orange" | "banana" = "orange"
```

- type → 型のエイリアス, interface

```typescript
type fruits = "apple" | "orange" | "banana" //型注釈の部分だけ変数のように格納できる。typeはオブジェクト型以外も指定可能
let fruit: fruits = "orange"

// typeで型を宣言するのと同じ。ただし,interfaceはオブジェクトのみ。末尾にコロンを忘れないように
interface User {
    id: number;
    name: string;
    greeting1(message: string): void;
    greeting2: (message: string) => void; //greeting1も2も同じ。書き方が違うだけ
}

// クラスに対してinterfaceで定義した形式を指定する
class Developer implements User {
    constructor(id: number, name: string)
    greeting1(message: string){
        return message
    }
    greeting2(message: string){
        return message
    }
}

interface Cpu = {
    version: number
}
interface Pc extends Cpu {
    memory: number;
    disk: number
}
```

- class

```typescript
class Person {
    name: string
    constructor(initName: string) {
        this.name = initName;
    }
    greeting() {
        console.log(`Hello ${this.name}`)
    }
}
const mike = new Person('Mike');
console.log(mike)

# 初期化の省略記述
class Person {
    constructor(public name: string, ) {
    }
}

```

- 関数

```typescript
// voidは何も返さない
function hello(): void {
    console.log("Hello")
}

function add( num1: number, num2: number): number {
    return num1 * num2

// (n1: number, n2: number) が引数の型注釈
// => number が返り値の型注釈。関数や変数の宣言時と違い、関数を保持する変数に型をつける場合は=>になる
const anotherAdd = add;
// 関数型
const anotherAdd: (n1: number, n2: number) => number = add;
const anotherAdd: (n1: number, n2: number): number = add;
```

- import, export

```typescript
import { target }  from "対象ファイル"              // 対象ファイルからインポートし、元の名前で使用
import { target as 任意の名前 }  from "対象ファイル"  // 対象ファイルからインポートし、任意の名前で使用
import * as 任意の名前   from "対象ファイル"          // 対象ファイルから全てインポートし、任意の名前で使用
import 任意の名前 from "対象ファイル"                 // export default文をimportする場合

export const target;                  // importされた時に元の名前で使用
export default const target;          // importされた時に任意の名前で使用できる
```

- 環境変数の利用

```typescript
console.log("process.env.対象変数")
```

- インデックスシグネチャ

```typescript
interface Designer {
    name: string;
    [index: string]: string;
}
```

- 型アサーション

- オーバーロード

```typescript
function toUpperCase(x: string): string
function toUpperCase(x: number): number
```

- !マークの意味 (Non-null assertion operator)

```typescript
return fruit! // fruitという値がundefinedやnullにはならないという意味
```

- optional Chaining -> ?が付いた対象がundefinedかnullであればundefinedを返す(エラーを出さず、undefinedで留める)。そうでなければそのままのデータを渡す。

```typescript
interface Designer {
    name?: {
        first: string;
        last: string;
    }
}
console.log(Designer.name?first)
```

- Nullish Coalescing -> undefinedかnullの場合のみ代入する

```typescript
# someObject.keyがundefinedかnullの場合のみ代入する
const testVar = someObject.key ?? 'tmpKey'
```

- lookup型

```typescript
interface Designer {
    name?: {
        first: string;
        last: string;
    }
}
# 変数名["内容"] で型にアクセスできる
type id = Designer["name"]["first"]
```
