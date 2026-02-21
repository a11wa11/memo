# VPN

Virtual Private Network。インターネット上に仮想的な専用回線を作る技術


## 用語

| 用語 | 説明 |
|-|-|
| VPNトンネル | 暗号化された通信経路。外から中身が見えない |
| VPNクライアント | VPN接続を開始する側 |
| VPNサーバー/ゲートウェイ | VPN接続を受け付ける側 |
| OpenVPN | オープンソースのVPNソフトウェア。.ovpnファイルで設定 |
| SSL-VPN | SSL/TLS暗号化を使ったVPN |
| DN情報 | Distinguished Name = 証明書の持ち主を識別する情報 |
| CA | Certificate Authority(認証局)。証明書を発行する機関 |
| CSR | Certificate Signing Request(証明書署名要求)。証明書を発行してもらうための「申請書」 |
| RSA | 公開鍵暗号方式の一つ |
| PKI | Public Key Infrastructure(公開鍵基盤)。デジタル証明書を発行・管理・検証するための仕組み全体を指す |
| CN | Common Name。証明書作成時にのみ定義する |
| SAN | Subject Alternative Name。証明書作成時にのみ定義する |
| 相互認証 | 高セキュリティ。クライアント側とサーバー側の両方が相手の身元を証明書で確認する認証方式 |
| 一方向認証 | クライアント側のみがサーバーを検証する方式 |
| TUN/TAP | VPN用の仮想ネットワークインターフェース。`tun0` など。TUNはL3(IP)、TAPはL2(Ethernet)を扱う |
| MTU | Maximum Transmission Unit。一度に送信できる最大パケットサイズ。標準は1500バイト |
| MSS / mssfix | TCPの最大セグメントサイズ。VPNの暗号化による肥大化を防ぐため、`mssfix` でサイズを制限し通信を安定させる |
|  |  |

## 関連パッケージ

### Easy-RSA

証明書を簡単に作成するためのツール。OpenSSLを直接使うと複雑なコマンドが必要だが、Easy-RSAは簡易化されている

```sh
# Easy-RSAをダウンロード
git clone https://github.com/OpenVPN/easy-rsa.git

# PKI初期化
cd easy-rsa/easyrsa3
./easyrsa init-pki

# CA(認証局)を作成
# Common Name: test-CA (任意の名前をつける)
./easyrsa build-ca nopass

# サーバー証明書を作成
./easyrsa build-server-full tmp-server.test nopass
# クライアント証明書を作成
./easyrsa build-client-full tmp-client1.test nopass
# ※サーバー/クライアント証明書はFQDN形式(sample.com)が好ましい

# 作成された証明書の場所
ls -l pki/ca.crt                       # CA証明書
ls -l pki/issued/tmp-server.test.crt   # サーバー証明書
ls -l pki/private/tmp-server.test.key  # サーバー秘密鍵
ls -l pki/issued/tmp-client1.test.crt  # クライアント証明書
ls -l pki/private/tmp-client1.test.key # クライアント秘密鍵
```

### openvpn

| ファイル | 説明 |
|-|-|
| client.key | 秘密鍵 |
| client.crt | クライアント証明書(身分証明書) |
| client.csr | 証明書の発行申請書 |
| ca.crt | CA(認証局)証明書(発行元の証明) |
| *.ovpn | OpenVPN設定ファイル |
|  |  |

#### コマンド

- 設定ファイルを指定して直接起動。デバッグ時や一時的な接続に利用

```sh
# サーバーまたはクライアントとして起動
openvpn --config /etc/openvpn/server/server.conf
openvpn --config /etc/openvpn/client/client.ovpn
```

- systemctlによる管理


```sh
# サービスの起動 (server.conf の場合)
sudo systemctl start openvpn-server@server

# 自動起動の設定
sudo systemctl enable openvpn-server@server

# ステータス確認
sudo systemctl status openvpn-server@server

# ログのリアルタイム確認
sudo journalctl -u openvpn-server@server -f
```

> `@server` の部分は`/etc/openvpn/server/`配下にある設定ファイル名（拡張子抜き）に対応します。
クライアントとして管理する場合は`openvpn-client@client`のようになる

- その他・有用なコマンド

| コマンド | 説明 |
|-|-|
| `openvpn --version` | バージョンとサポートされている暗号化アルゴリズム等を確認 |
| `openvpn --genkey --secret ta.key` | TLS-Auth用の共有鍵（静的鍵）を生成 |
| `openvpn --show-ciphers` | 利用可能な暗号化方式一覧を表示 |
| `openvpn --show-digests` | 利用可能なメッセージ認証（HMAC）一覧を表示 |

##### トラブルシューティング

- **ルート情報の確認**: `ip route` でVPN経由のルートが正しく追加されているか確認
- **インターフェース確認**: `ip addr` または `ifconfig` で `tun0` などの仮想デバイスが作成されているか確認
- **MTUサイズの調整**: 通信が不安定な場合、設定ファイルに `mssfix 1430` などを追記して調整

#### ovpnファイル

```text
<ca>
-----BEGIN CERTIFICATE-----
(ca.crtの中身)
-----END CERTIFICATE-----
</ca>

<cert>
-----BEGIN CERTIFICATE-----
(client.crtの中身)
-----END CERTIFICATE-----
</cert>

<key>
-----BEGIN PRIVATE KEY-----
(client.keyの中身)
-----END PRIVATE KEY-----
</key>

<tls-auth>
-----BEGIN OpenVPN Static key V1-----
(ta.keyの中身)
-----END OpenVPN Static key V1-----
</tls-auth>
key-direction 1
```
