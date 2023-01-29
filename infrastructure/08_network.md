# ネットワーク

| OSI参照モデル | TCP/IPモデル | 例 |　通信規約 |
| - | - | - | -|
| アプリケーション層 | アプリケーション層 | メール、web閲覧 | HTTP, HTTPS, POP, SMTP, SSH, DNS, FTP |
| プレゼンテーション層 | ゛ | 文字コード・圧縮・データ暗号/複合など文字の送り方を決める |  |
| セッション層 | ゛ | アプリケーション感の一連の継続的な処理を規定（ログイン状態持続のこと） |  |
| トランスポート層 | トランスポート層 | ポート番号の割当などセッションを開始するための事前の到着順序確認 | TCP, UDP |
| ネットワーク層 | ネットワーク層 | IPアドレスの割当、ルーティング方法を規定 | IP, ICMP, ARP, BGP |
| データリンク層 | 物理層 | MACアドレスによるノード間の通信 | Ethernet, PPP |
| 物理層 | ゛ | ビットによるコンピュータ間の物理的なデータ伝送形式を規定 |  |
|  |  |  |  |

## HTTPヘッダー

### User-Agentとは

Webブラウザの固有情報。ブラウザーなどの種類を表す情報で、サーバーはブラウザーなどの種類によっても、異なる結果を返す可能性がある

### Referrerとは

直前にリンクされていたURL

* sarのインストール

```sh
yum install sysstat
```

* sarの使い方

```sh
vi /etc/cron.d/sysstat

sar(sysstat)によるボトルネック参照
sarコマンドの使い方 QIITA
sarコマンドの基本
```

* sar -h ヘルプの表示
* sar -r メモリ使用率
* sar -R

* sar -b I/Oと転送率の状況

* iptables

```sh
# yum install iptables

iptables -A INPUT -p tcp -d 対象サーバIP --dport 3000(ポート番号) -j DROP

https://knowledge.sakura.ad.jp/4048/

iptablesには、INPUT、OUTPUT、FORWORDという3つのポリシーがあります。それぞれの通信の基本ポリシーを”DROP”(拒否)、”ACCEPT”(許可)のどちらかに設定できます。
INPUTは、サーバーに入ってくる通信のポリシーです。ここでは基本ポリシーを”DROP”にして、あとで個別のポートに対して許可する設定にします。
OUTPUTは、サーバーから出て行く通信のポリシーです。基本ポリシーは”ACCEPT“にします。
FORWARDは、受信したデータを他のサーバーへ転送する際に適用される設定です。ここでは、特に転送するサーバーは無いので”DROP”にします。

```
