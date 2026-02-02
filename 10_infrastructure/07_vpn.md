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
|  |  |

## openvpn

| ファイル | 説明 |
|-|-|
| client.key | 秘密鍵 |
| client.crt | クライアント証明書(身分証明書) |
| client.csr | 証明書の発行申請書 |
| ca.crt | CA(認証局)証明書(発行元の証明) |
| *.ovpn | OpenVPN設定ファイル |
|  |  |
|  |  |
|  |  |
