# CSS

- [CSS](#css)
  - [適用方法](#適用方法)
  - [詳細度](#詳細度)
    - [プロパティ](#プロパティ)
  - [構文](#構文)
  - [Sass](#sass)
    - [文法](#文法)

## 適用方法

cssを適用するには主に以下の3つの手法がある

1. styleタグを使用
1. タグ内にstyleで定義
1. 別のcssファイルで定義する

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <!-- 3の方法 -->
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <!-- 2の方法 -->
  <span style="background-color: pink;">HELLO!!</span>
  <!-- 1の方法 -->
  <style>
    span {
      width: 100px;
    }
  </style>
</body>
</html>

```

## 詳細度

cssには詳細度という概念があり、重複記述があった場合に詳細度が高い方が優先される。  
例としてspanよりもclass指定、class指定よりもid指定の方が詳細度が高い

- インライン要素とブロック要素
  - インライン要素は横並びに配置される
    - `<span>` など
    - 横幅と縦幅を指定できない
    - `display: block;`でブロック要素のように振る舞える
    - `text-align: left|center|right;`で文字の整列を指定
  - ブロック要素は縦並びに配置される
    - `<div>` など
    - 横幅と縦幅を指定できる
    - `display: inline;`でインライン要素のように振る舞える
    - `margin: 0 auto;`で文字の整列を中央揃えで指定。text-alignは無効
  - `display: inline-block;`でインライン要素とブロック要素の中間のように振る舞える
    - 高さは反映されるが、横並びで要素が整列することになる

### プロパティ

```css
/* class要素を指定する時は.(ドット)をつける */
.first
/* id要素を指定する時は#(シャープ)をつける */
#first
```

## 構文

- ボタンホバー

```css
.btn {
  background-color: white;
  color: black;
  border: 1px solid black;
  padding: 10px 40px;
  margin: 50px 0;
  font-weight: 600;
  cursor: pointer;
  transition: all 3s;
}

.btn:hover {
  background-color: pink;
  color: yellowgreen;
}
```

## [Sass](https://sass-lang.com/)

- cssを便利に記述できるもの
  - 変数が定義可能
  - ネストが可能(ブロックの中にブロックを記述)
- ブラウザはcssは読み込めるがscssは読み込めない

<table>
  <tr>
    <th>scss</th>
    <th>css</th>
  </tr>
  <tr>
    <td><code><pre>
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}</code></td>
    <td><code><pre>
  body {
    font: 100% Helvetica, sans-serif;
    color: #333;
  }
</code></td>
  </tr>
  <tr>
    <td><code><pre>
nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block

  a
    display: block
    padding: 6px 12px
    text-decoration: none
      </code></td>
    <td><code><pre>
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
</code></td>
  </tr>
</table>

### 文法

- &(アンパサンド)
  - 親のセレクタを`&`に代入できる
    - ただし、スペースは含まれない

```html
<div class="parent">
    親要素
    <div class="element">子要素</div>
    <div class="element second-child">子要素</div>
    <div class="element">
        子要素
        <div class="element">孫要素</div>
    </div>
</div>
```

- 上記htmlを対象にしたscss例

```scss
.parent {
  background-color: blue;
  /* .parent .element と同じ意味 */
  & .element {
    background-color: purple;
    /* >は１階層下のみの範囲となる。>がなければ下階層全てを範囲とする */
    & > .element {
      background-color: green;
    }
    /*
      .parent の中の.element と .second-child のクラスを持つタグを範囲とする
      スペースがない場合は二つのタグを併せ持つと言う意味
    */
    &.second-child {
      background-color: pink;
    }
  }
}
```
