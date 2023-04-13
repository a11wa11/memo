# HTML

- [HTML](#html)
  - [構文](#構文)
    - [リンク関連](#リンク関連)
    - [フォーム](#フォーム)
    - [テーブル](#テーブル)
    - [折り畳み](#折り畳み)
    - [色](#色)
  - [エディタでの使い方](#エディタでの使い方)

## 構文

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  aaaaaaaa
</body>
</html>
```

### リンク関連

リンク作成

- 基本

```html
<a href="ここにURL">ここにリンクテキスト</a>
```

- aタグに`target=”_blank”`属性をつけることで、リンクを押下するとタブで開くようにする

```html
<a href="ここにURL" target="_blank">ここにリンクテキスト</a>
```

上記の例を書いたものが<a href="https://github.com/a11wa11/memo" target="_blank">これ</a>

- 作成したリンクを押下すると写真を開く

```html
<A href="next.jpg">次の絵</A>
```

- ファイルをダウンロード

```html
<A href="next.zip">ファイルをダウンロード</A>
```

- FTPサーバー上のファイルをダウンロード

```html
<A href="ftp://ftp.company.com/some/next.zip">
```

- リンク部分にマウス・カーソルを載せるとポップアップで文字が表示される → title属性を使用

```html
<A href="https://github.com/a11wa11/memo" title="テスト">次のページ</A>
```

上記の例を書いたものが<a href="https://github.com/a11wa11/memo" title="テスト">これ</a>

- 整形済みテキスト(pre)

例

<pre>
  HTMLのpreタグは、"preformatted text"（整形済みテキスト）を表すために使用される要素です。
  preタグ内のテキストは、そのままのスペース、改行、およびインデントが保持され、ブラウザによってそのまま表示されます。
</pre>

---

コード

```html
<pre>
  HTMLのpreタグは、"preformatted text"（整形済みテキスト）を表すために使用される要素です。
  preタグ内のテキストは、そのままのスペース、改行、およびインデントが保持され、ブラウザによってそのまま表示されます。
</pre>
```

### フォーム

例

<form method="GET" action="対象ファイル.php">
指名
<input type="text" name="対象変数">
<input type="submit" value="送信">
</form>

---

コード

```html
// form作成
<form method="GET" action="対象ファイル.php">
指名
<input type="text" name="対象変数">
<input type="submit" value="送信">
</form>

# input.php?you_name=入力名 のようにURLの末尾にGETメソッドの内容が表示される(クエリ、クエリストリング)
```

### テーブル

例

<table>
  <tr>
    <th>ヘッダー1</th>
    <th>ヘッダー2</th>
  </tr>
  <tr>
    <td><code>項目1</code></td>
    <td><code>項目2</code></td>
  </tr>
</table>

---

コード

```html
<table>
  <tr>
    <th>ヘッダー1</th>
    <th>ヘッダー2</th>
  </tr>
  <tr>
    <td><code>項目1</code></td>
    <td><code>項目2</code></td>
  </tr>
</table>
```

### 折り畳み

例

<details>
    <summary>ここを展開</summary>
    Something small enough to escape casual notice.
</details>

---

コード

```html
<details>
    <summary>展開</summary>
    Something small enough to escape casual notice.
</details>
```

### 色

例

<font color="red">赤色テキスト</font><br>
<font color="green">緑色テキスト</font><br>
<font color="blue">青色テキスト</font><br>
<font color="yellow">黄色テキスト</font><br>
<font color="purple">紫色テキスト</font><br>

---

コード

```html
<font color="red">赤色テキスト</font><br>
<font color="green">緑色テキスト</font><br>
<font color="blue">青色テキスト</font><br>
<font color="yellow">黄色テキスト</font><br>
<font color="purple">紫色テキスト</font><br>
```

## エディタでの使い方

- vscodeでhtmlファイルを作成
  - < ! + tab > で保管可能
