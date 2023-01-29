# nginx

* [locationディレクティブ](https://heartbeats.jp/hbblog/2012/04/nginx05.html)

| なし | 前方一致 |
| ^~ | 前方一致。一致したら、正規表現の条件を評価しない |
| = | 完全一致。パスが等しい|
| ~ | 正規業限(大文字・小文字を区別する) |
| ~* | 正規業限(大文字・小文字を区別しない) |

[優先順位参考](https://muziyoshiz.hatenablog.com/entry/2019/06/30/203903)

## リダイレクト設定

```nginx
# 301の場合 恒久リダイレクト
location ^~ /foo/bar.html {
  rewrite ^(.*)$ /http://example.com/foo/ permanent:
}

# 302の場合 一時的リダイレクト
location ^~ /foo/bar.html {
  rewrite ^(.*)$ /http://example.com/hoge/foo.html redirect:
}
```

## proxy_pass

```nginx
# 301の場合 恒久リダイレクト
location  /name/ {
  proxy_pass /http://example.com/;
}
```
