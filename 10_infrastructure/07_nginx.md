# nginx

- [nginx](#nginx)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
  - [構文](#構文)
    - [変数](#変数)
    - [ディレクティブ](#ディレクティブ)
      - [location](#location)
      - [server](#server)
    - [リダイレクト設定](#リダイレクト設定)
    - [proxy\_pass](#proxy_pass)
    - [stream](#stream)
    - [upstream](#upstream)

## インストール

- centos7

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

## コマンド

```sh
# シンタックス確認
nginx -t
# シンタックス確認して現在適用されている設定(include含めて)を出力
nginx -T

# 強制停止
nginx -s stop

# 現在処理中のリクエストが完了するのを待ってから停止
nginx -s quit

# 設定再読込
nginx -s reload

# ログファイルを再オープンする。ログファイルのローテーション（例: ログファイルをアーカイブして新しいログファイルを作成する）後に使用され、Nginxに新しいログファイルを使用させるために使われる
nginx -s reopen
```

## 構文

### 変数

- setディレクティブ

```nginx
server {
    listen 80;
    server_name example.com;

    set $backend_url "http://backend.example.com";

    location / {
        proxy_pass $backend_url;
    }
}
```

- envディレクティブ

```nginx
env BACKEND_HOST;

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://$BACKEND_HOST;
    }
}
```

### ディレクティブ

#### [location](https://heartbeats.jp/hbblog/2012/04/nginx05.html)

| 正規表現 | 内容 |
|-|-|
| なし | 前方一致 |
| ^~ | 前方一致。一致したら、正規表現の条件を評価しない |
| = | 完全一致。パスが等しい|
| ~ | 正規業限(大文字・小文字を区別する) |
| ~* | 正規業限(大文字・小文字を区別しない) |

[優先順位参考](https://muziyoshiz.hatenablog.com/entry/2019/06/30/203903)

rootなどが複数の設定ファイルで定義されていると、includeで後から読み込まれたファイルが基本は優先的に反映される

#### [server](https://nginx.org/en/docs/http/server_names.html)

- listen
  - 一つのサーバーブロックで複数のlistenディレクティブを指定し、複数のポートでリクエストを受け付けることが可能
- default_server
  - そのポートへのリクエストが他のserver_nameに一致しない場合に、このサーバーブロックが選ばれるようになる
- server_name
  - サーバーブロックをどのドメイン名で識別するかを設定する

```nginx
server {
    listen       80;
    listen       8080  default_server; # default_server
    server_name  example.net;
}

server {
    listen       80  default_server;
    listen       8080;
    server_name  example.org;
}

```

### リダイレクト設定

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

### proxy_pass

```nginx
# 301の場合 恒久リダイレクト
location  /name/ {
  proxy_pass /http://example.com/;
}
```

### stream

TCPやUDPプロトコルを使用したリクエストを他のサーバーにプロキシする。これにより、HTTP以外のプロトコルを使用するアプリケーションもnginx経由でアクセス可能

- ロードバランシング
  - 複数のバックエンドサーバーにトラフィックを分散させることで、負荷分散を行い、システムの可用性とスケーラビリティを向上させる
- セキュリティとアクセス制御
  - Nginxを通じてアクセスすることで、特定のIPアドレスに対するアクセス制限や、TLS/SSLの暗号化を行える

```nginx
stream {
    server {
        listen     12345;
        proxy_pass https://転送先URL:443(ポート);
    }
}
```

### [upstream](https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/)

upstreamは複数のバックエンドサーバーに対するロードバランシングやフェイルオーバーの設定を可能にする重要なディレクティブ  
システムの可用性を高め、スケールアウトのアプローチを取ることができる

- 役割
  - ロードバランシング
    - リクエストを複数のバックエンドサーバーに分散させることができる。nginxは、ラウンドロビン、最小接続数、IPハッシュなど、さまざまなアルゴリズムを使用してリクエストを分散させる
  - フェイルオーバー
    - あるサーバーが応答しない場合、nginxは他のサーバーにリクエストを転送して、サービスの継続性を確保する
  - ヘルスチェック
  - nginxは、各バックエンドサーバーの状態をモニタリングし、ダウンしているサーバーにはリクエストを送らないようにすることも可能

```nginx
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
```
