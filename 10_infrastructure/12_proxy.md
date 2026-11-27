# プロキシ

## プロキシとは

プロキシは、クライアントの代わりに接続先サーバーへリクエストを送る中継サーバーである。

一般的なプロキシパッケージであるSquidは、クライアントからインターネット上の接続先への通信を中継する「フォワードプロキシ」である。

```text
クライアント
  -> Squidで認証
  -> 許可された宛先か確認
  -> 接続先へ通信を中継
  -> 応答をクライアントへ返す
```

HTTPSの場合、クライアントはSquidへ`CONNECT host:443`を送り、Squidを通じて接続先とのTLS通信を行う。  
Squidが`200 Connection established`を返し、そのあと接続先が`HTTP/2 200`などを返せば通信成功である。  
`407 Proxy Authentication Required`はSquidの認証エラーである。接続先のWebサーバーが返す`401 Unauthorized`とは別の段階のエラーである。

### インストール

```bash
dnf install -y squid httpd-tools
```

`dnf`でSquidとApacheの認証ファイル操作ツール`htpasswd`をインストールする。`-y`は確認プロンプトに自動で「yes」と答えるオプションである。

## 設定ファイルの役割

- `/etc/squid/passwords`
  - SquidのBasic認証で使うユーザー名とパスワードハッシュを保存するファイルである。平文パスワードは保存しない。

```text
ユーザー名:<パスワードハッシュ>
```

Squidの`basic_ncsa_auth`ヘルパーがこのファイルを照合し、認証可否を判定する。ファイルにはパスワードハッシュが含まれるため、所有者と読み取り権限を制限する。

- `/etc/squid/squid.conf`
  - Squidが待ち受けるポート、認証方法、許可する通信などを定義する設定ファイルである

### `squid.conf`の設定内容

```conf
http_port 3128                                                         # TCPポート`3128`でプロキシ接続を待ち受ける

auth_param basic program /対象パス/basic_ncsa_auth /etc/squid/passwords # Basic認証を使い、指定した認証ヘルパーに`/etc/squid/passwords`を照合させる
auth_param basic realm 識別名                                           # 認証要求時にクライアントへ示す識別名を定義する
auth_param basic casesensitive on                                      # ユーザー名の大文字・小文字を区別する
acl authenticated proxy_auth REQUIRED                                  # 認証に成功した利用者を`authenticated`というACLとして扱う

acl allowed_dst dstdomain 対象ドメイン(google.comなど)                  # 中継を許可する接続先ドメインをACLとして定義する
acl SSL_ports port 443                                                 # HTTPSで一般的に使うポート`443`を`SSL_ports`として定義する
acl CONNECT method CONNECT                                             # HTTPSトンネルに使われる`CONNECT`メソッドをACLとして定義する

http_access deny CONNECT !SSL_ports                                    # ポート`443`以外への`CONNECT`を拒否する
http_access allow authenticated allowed_dst                            # 認証済で許可ドメインにも一致する通信を許可する
http_access deny all                                                   # 上記で許可されなかった通信をすべて拒否する

cache deny all                                                         # Squidにキャッシュさせない
```

- `basic_ncsa_auth`は、`rpm -ql squid`から認証ヘルパーのパスを調べて設定する

- 認証ヘルパーを検索する

```bash
NCSA_AUTH="$(rpm -ql squid | grep -m1 basic_ncsa_auth)"
```

- `rpm -ql squid`: `squid`パッケージが配置したファイルを一覧表示する
- `grep -m1 basic_ncsa_auth`: 名前に`basic_ncsa_auth`を含む最初の行を取得する
- `|`: 左側の出力を右側のコマンドへ渡す
- `$(...)`: 検索結果を`NCSA_AUTH`へ代入する

## コマンド

### 認証ファイルを作成する

- `htpasswd`でユーザーとパスワードハッシュをファイルへ書き込む。

```bash
htpasswd -b -c /etc/squid/passwords "$PROXY_USER" "$PROXY_PASS"
```

- `-b`: パスワードをコマンドの引数として渡す
- `-c`: ファイルを新規作成する。既存ファイルがあると上書きする
- `/etc/squid/passwords`: 出力先
- `"$PROXY_USER"`: ユーザー名
- `"$PROXY_PASS"`: パスワード

`-b`ではパスワードが一時的にプロセス引数に現れる可能性がある。`-c`は既存ファイルを上書きするため、ユーザーを追加する目的では使わない。

- `htpasswd`で確認・更新する

```bash
htpasswd -v /etc/squid/passwords ユーザー名
```

`-v`は入力したパスワードが登録値と一致するかを検証するオプション。ファイルは更新しない。

```bash
htpasswd /etc/squid/passwords ユーザー名
```

対話形式でパスワードを入力し、登録値を更新する。更新後はSquidを再起動して再試行する。


- Squidを起動し、自動起動を設定する

```bash
systemctl enable --now squid
```

- `systemctl`: systemdでサービスを管理するコマンド
- `enable`: OS起動時にSquidを自動起動する設定にする
- `--now`: 自動起動設定に加え、今すぐSquidを起動する
- `squid`: 対象のサービス名

### 設定確認・運用コマンド

- 設定ファイルの構文を確認する

```bash
sudo squid -k parse
```

Squidの設定ファイルを解析して構文エラーを確認する。設定を反映したり、サービスを再起動したりはしない。通常は既定の`/etc/squid/squid.conf`を読み込む。

- 設定を再解析し、サービスを再起動する

```bash
systemctl restart squid
```

先に構文を確認してから再起動する。`restart`は稼働中のSquidを停止して起動し直す

### サービス状態とログを確認する

```bash
sudo systemctl status squid --no-pager
sudo journalctl -u squid -n 50 --no-pager
sudo tail -f /var/log/squid/access.log
```

- `systemctl status`: サービスの起動状態を表示する
- `journalctl -u squid`: Squidサービスのログを表示する
- `tail -f`: アクセスログの末尾を継続表示する

認証ユーザーは`access.log`に記録される。今回の構成では`E2E-CI`のように大文字・小文字をそろえる。  
`TCP_DENIED/407`はプロキシ認証またはアクセス制御で拒否された記録である。

### プロキシ経由で接続を確認する

```bash
curl -x "http://ユーザー名:${PROXY_PASS}@127.0.0.1:3128" -I https://対象ドメイン.com/
```

- `-x`: 使用するプロキシを指定する
- `-I`: 本文を取得せず、HTTPヘッダーを確認するリクエストを送る
- `127.0.0.1:3128`: コマンドを実行したサーバー自身のSquidを指定する
- `${PROXY_PASS}`: 現在のシェルに設定したパスワード

`HTTP/1.1 200 Connection established`のあとに接続先の`HTTP/2 200`などが返れば成功である。パスワードをURLへ直接書かない。シェル履歴やプロセス引数に残る可能性がある。

## Bashでパスワードを非表示入力する

```bash
read -r -s -p 'PROXY_PASS: ' PROXY_PASS
printf '\n'
```

- `read -s`は入力した文字を画面に表示しない。入力後、同じシェルから`curl`を実行できる。別プロセスへ環境変数として渡す場合は`export PROXY_PASS`を使う
