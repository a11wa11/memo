# nginx

## インストール

* centos7

```sh
touch /etc/yum.repos.d/nginx.repo
echo '[nginx-stable]' >> /etc/yum.repos.d/nginx.repo
echo 'name=nginx stable repo' >> /etc/yum.repos.d/nginx.repo
echo 'baseurl=http://nginx.org/packages/centos/$releasever/$basearch/' >> /etc/yum.repos.d/nginx.repo
echo 'gpgcheck=1' >> /etc/yum.repos.d/nginx.repo
echo 'enabled=1' >> /etc/yum.repos.d/nginx.repo
echo 'gpgkey=https://nginx.org/keys/nginx_signing.key' >> /etc/yum.repos.d/nginx.repo
echo 'module_hotfixes=true' >> /etc/yum.repos.d/nginx.repo
yum install -y nginx
```

## [locationディレクティブ](https://heartbeats.jp/hbblog/2012/04/nginx05.html)

| 正規表現 | 内容 |
|-|-|
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

## [upstream](https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/)

```nginx
stream {
    server {
        listen     12345;
        proxy_pass https://転送先URL:443(ポート);
    }
}
```
