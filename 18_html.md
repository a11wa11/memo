# HTML

## リンク関連

* リンク作成

```html
<a href="ここにURL">ここにリンクテキスト</a>
```

* 作成したリンクを押下するとタブで開くようにする　→　aタグに`target=”_blank”`属性をつける

```html
<a href="ここにURL" target="_blank">ここにリンクテキスト</a>
```

上記の例を書いたものが<a href="https://github.com/a11wa11/memo" target="_blank">これ</a>

* 作成したリンクを押下すると写真を開く

```html
<A href="next.jpg">次の絵</A>
```

* ファイルをダウンロード

```html
<A href="next.zip">ファイルをダウンロード</A>
```

* FTPサーバー上のファイルをダウンロード

```html
<A href="ftp://ftp.company.com/some/next.zip">
```

* リンク部分にマウス・カーソルを載せるとポップアップで文字が表示される → title属性を使用

```html
<A href="https://github.com/a11wa11/memo" title="テスト">次のページ</A>
```

上記の例を書いたものが<a href="https://github.com/a11wa11/memo" title="テスト">これ</a>

## エディタでの使い方

* vscodeでhtmlファイルを作成
  * < ! + tab > で保管可能
