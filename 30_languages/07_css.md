# CSS

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

## 構文

```css
/* class要素を指定する時は.(ドット)をつける */
.first
/* id要素を指定する時は#(シャープ)をつける */
#first
```

* 詳細度

cssには詳細度という概念があり、重複記述があった場合に詳細度が高い方が優先される。  
例としてspanよりもclass指定、class指定よりもid指定の方が詳細度が高い

* インライン要素とブロック要素
  * インライン要素は横並びに配置される
    * `<span>` など
    * 横幅と縦幅を指定できない
    * `display: block;`でブロック要素のように振る舞える
    * `text-align: left|center|right;`で文字の整列を指定
  * ブロック要素は縦並びに配置される
    * `<div>` など
    * 横幅と縦幅を指定できる
    * `display: inline;`でインライン要素のように振る舞える
    * `margin: 0 auto;`で文字の整列を中央揃えで指定。text-alignは無効
  * `display: inline-block;`でインライン要素とブロック要素の中間のように振る舞える
    * 高さは反映されるが、横並びで要素が整列することになる
