# HTML

方法は簡単で、だけ。例として以下のコードのようにすれば良い。
* リンク作成

```
<a href="ここにURL">ここにリンクテキスト</a>
```
  * 作成したリンクを押下するとタブで開くようにする　→　aタグに`target=”_blank”`属性をつける

```
<a href="ここにURL" target="_blank">ここにリンクテキスト</a>
```

上記の例を書いたものが<a href="https://github.com/a11wa11/memo" target="_blank">これ</a>

  * 作成したリンクを押下すると写真を開く

```
<A href="next.jpg">次の絵</A>
```

  * 圧縮したファイルをダウンロード

```
<A href="next.zip">ファイルをダウンロード</A>
```

  * FTPサーバー上のファイルをダウンロード

```
<A href="ftp://ftp.company.com/some/next.zip">
```

  * リンク部分にマウス・カーソルを載せるとポップアップで文字が表示される → title属性を使用

```
<A href="https://github.com/a11wa11/memo" title="テスト">次のページ</A>
```
上記の例を書いたものが<a href="https://github.com/a11wa11/memo" title="テスト">これ</a>
