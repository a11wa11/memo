# ネットワーク

- [ネットワーク](#ネットワーク)
  - [OSI](#osi)
  - [URL](#url)
  - [HTTP](#http)
    - [HTTPヘッダー](#httpヘッダー)
      - [User-Agentとは](#user-agentとは)
      - [Referrerとは](#referrerとは)
  - [システム情報](#システム情報)
    - [ホストネーム](#ホストネーム)
    - [タイムゾーン](#タイムゾーン)
    - [ロケール](#ロケール)
    - [IPアドレス](#ipアドレス)
      - [ip](#ip)
      - [iptables](#iptables)
      - [ping](#ping)
      - [ネットワーク設定を反映](#ネットワーク設定を反映)
    - [ポート](#ポート)
    - [ネットワークマネージャー](#ネットワークマネージャー)
    - [名前解決](#名前解決)
    - [nice値](#nice値)
    - [稼働時間](#稼働時間)
    - [ファイル系](#ファイル系)
      - [ファイルタイプ確認](#ファイルタイプ確認)
      - [ファイル上限数確認](#ファイル上限数確認)
      - [md5sum](#md5sum)
    - [サービス](#サービス)
  - [パフォーマンス測定](#パフォーマンス測定)
    - [vmstat](#vmstat)
    - [netstat](#netstat)
    - [ss](#ss)
    - [lsof](#lsof)
    - [sar](#sar)
      - [インストール](#インストール)
      - [コマンド例](#コマンド例)
    - [CPU](#cpu)
      - [top(CPU、メモリ、プロセスの状態表示)](#topcpuメモリプロセスの状態表示)
      - [htop](#htop)
      - [lscpu](#lscpu)
    - [メモリ](#メモリ)
    - [ディスク](#ディスク)
      - [df](#df)
      - [du](#du)
      - [lsblk](#lsblk)
      - [パーティション拡張](#パーティション拡張)
      - [ファイルシステム拡張](#ファイルシステム拡張)
  - [CPUアーキテクチャ](#cpuアーキテクチャ)

## OSI

| OSI参照モデル | レイヤー番号 | TCP/IPモデル | 例 |　通信規約 |
| - | - | - | - | - |
| アプリケーション層 | 7 | アプリケーション層 | メール、web閲覧 | HTTP, HTTPS, POP, SMTP, SSH, DNS, FTP |
| プレゼンテーション層 | 6 | ゛ | 文字コード・圧縮・データ暗号/複合など文字の送り方を決める |  |
| セッション層 | 5 | ゛ | アプリケーション感の一連の継続的な処理を規定（ログイン状態持続のこと） |  |
| トランスポート層 | 4 | トランスポート層 | ポート番号の割当などセッションを開始するための事前の到着順序確認 | TCP, UDP |
| ネットワーク層 | 3 | ネットワーク層 | IPアドレスの割当、ルーティング方法を規定 | IP, ICMP, ARP, BGP |
| データリンク層 | 2 | 物理層 | MACアドレスによるノード間の通信 | Ethernet, PPP |
| 物理層 | 1 | ゛ | ビットによるコンピュータ間の物理的なデータ伝送形式を規定 |  |
|  |  |  |  |  |

---

## URL

WHATWG URLの構造

```sh
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                                            href                                             │
├──────────┬──┬─────────────────────┬─────────────────────┬───────────────────────────┬───────┤
│ protocol │  │        auth         │        host         │           path            │ hash  │
│          │  │                     ├──────────────┬──────┼──────────┬────────────────┤       │
│          │  │                     │   hostname   │ port │ pathname │     search     │       │
│          │  │                     │              │      │          ├─┬──────────────┤       │
│          │  │                     │              │      │          │ │    query     │       │
"  https:   //    user   :   pass   @ sub.host.com : 8080   /p/a/t/h  ?  query=string   #hash "
│          │  │          │          │   hostname   │ port │          │                │       │
│          │  │          │          ├──────────────┴──────┤          │                │       │
│ protocol │  │ username │ password │        host         │          │                │       │
├──────────┴──┼──────────┴──────────┼─────────────────────┤          │                │       │
│   origin    │                     │       origin        │ pathname │     search     │ hash  │
├─────────────┴─────────────────────┴─────────────────────┴──────────┴────────────────┴───────┤
│                                            href                                             │
└─────────────────────────────────────────────────────────────────────────────────────────────┘
```

## HTTP

### HTTPヘッダー

#### User-Agentとは

Webブラウザの固有情報。ブラウザーなどの種類を表す情報で、サーバーはブラウザーなどの種類によっても、異なる結果を返す可能性がある

#### Referrerとは

直前にリンクされていたURL

---

## システム情報

- [ホストネーム](#ホストネーム)
- [タイムゾーン](#タイムゾーン)
- [ロケール](#ロケール)
- [IPアドレス](#ipアドレス)
- [ポート](#ポート)
- [ネットワークマネージャー](#ネットワークマネージャー)
- [名前解決](#名前解決)
- [nice値](#nice値)
- [稼働時間](#稼働時間)
- [ファイル系](#ファイル系)
- [サービス](#サービス)

### ホストネーム

- ホスト名を確認

```sh
# 以下どのコマンドも確認可能
cat /etc/hostname  
hostnamectl status
hostname
getent hosts
```

- ホスト名変更

```sh
# 以下どちらでもOK
hostnamectl set-hostname ホストネーム名
hostname 'ホストネーム名'
```

- ローカルで自分専用で名前解決したい場合

[こちら](https://qiita.com/Hikosaburou/items/085a3fbcbd5cd04e834d)を参考

```sh
# macで操作前提
# /private/etc/hostsに記述

### ALBなどの任意のIPを登録
111.111.xxx.yyy example.com
```

### タイムゾーン

- タイムゾーンを確認

```sh
timedatectl status
cat /etc/localtime
```

- タイムゾーン変更

```sh
# まずはバックアップ
cp /etc/localtime /etc/localtime.org
# 以下どれらかでOK。元ファイルはどちら(Asia/TokyoもJapan)も同じ
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
cp /usr/share/zoneinfo/Japan /etc/localtime
timedatectl set-timezone Asia/Tokyo
```

### ロケール

```sh
# ロケールを確認
locale
localectl

# ロケール変更するための日本語言語パックのインストール
yum install glibc-langpack-ja  # amazonlinux2やcentos7など
dnf install langpacks-ja       # amazonlinux2023やcentos8など

# 使用可能なロケール一覧
locale -a

# ロケール変更パターン1
export LC_ALL=ja_JP.UTF-8
# ロケール変更パターン2
echo "LANG=ja_JP.UTF-8" > /etc/sysconfig/i18n
# ロケール変更パターン3
localectl set-locale LANG=ja_JP.UTF-8
```

### IPアドレス

- グローバルIPを確認

```sh
curl httpbin.org/ip
```

#### ip

```sh
# IPアドレスの確認
ip a
ip addr show
ifconfig # 古くて今はあまり推奨されていない
hostname -i|-I

# 下記のファイルのIPADDRの変更などでIPアドレスを変更可能
/etc/sysconfig/network-scripts/ifcfg* # OSのNICに関する定義を行うファイル

$ cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0                        # 物理デバイス名
BOOTPROTO=dhcp                     # プロトコル
ONBOOT=yes                         # ブート時にこのデバイスをONにするかどうか
BROADCAST=192.168.0.255            # ブロードキャストアドレス
IPADDR=192.168.0.125               # IPアドレス
NETMASK=255.255.255.0              # ネットマスク
NETWORK=192.168.0.0                # ネットマスク
```

- `/etc/sysconfig/network` (RedHat系OS)でネットワークに接続する際に必要とされる情報が記述されている。  マシン全体のネットワーク設定が記述されている。
- `/etc/sysconfig/network-scripts/ifcfg-[NIC名]`はNICごとのネットワーク設定が記述されている

```sh
$ cat /etc/sysconfig/network
NETWORKING=yes              # ネットワーキングを有効にするかどうか
HOSTNAME=your.domain.name   # ホスト名
DOMAINNAME=your.domain.name # ドメイン名
GATEWAY=192.168.0.1         # デフォルトゲートウェイ
NOZEROCONF=yes
```

#### iptables

[参考](https://knowledge.sakura.ad.jp/4048/)

- iptablesには、INPUT、OUTPUT、FORWARDという3つのポリシーがあります。それぞれの通信の基本ポリシーを”DROP”(拒否)、”ACCEPT”(許可)のどちらかに設定できます。
- INPUTは、サーバーに入ってくる通信のポリシーです。ここでは基本ポリシーを”DROP”にして、あとで個別のポートに対して許可する設定にします。
- OUTPUTは、サーバーから出て行く通信のポリシーです。基本ポリシーは”ACCEPT“にします。
- FORWARDは、受信したデータを他のサーバーへ転送する際に適用される設定です。ここでは、特に転送するサーバーは無いので”DROP”にします。

```sh
# インストール
yum install iptables

# 特定のアクセスを制限したり、逆に許可したりし、その制限を確認する
iptables -L
# INPUTのみ採番して制限内容を表示する
iptables -nL INPUT --line-numbers
# 制限するときの例
iptables -A INPUT -p tcp -d 対象サーバIP --dport 3000(ポート番号) -j DROP
iptables -A INPUT -p tcp -s 10.0.0.0(IPアドレス) --sport 3000(ポート番号) --tcp-flags SYN,FIN,RST,ACK FIN,ACK -j DROP
iptables -A INPUT -p tcp -s 10.0.0.0(IPアドレス) --sport 3000(ポート番号) --tcp-flags PSH,ACK PSH,ACK -j DROP
```

#### ping

- ネットワークの疎通を確認する
- ICMPを使用している
- 対象ホストでICMP受信許可がされていないければping応答が返らない
- OSI参照モデルのネットワークそう(IPレベル)までが確認できる範囲
- pingが通ればサーバがシャットダウンされておらずネットワークにも問題ないことがわかり、アプリケーション側などで問題がある可能性ありと切り分け可能

#### ネットワーク設定を反映

```sh
systemctl restart network
```

### ポート

```sh
netstat -an | grep ポートナンバー
ss | grep ポートナンバー
lsof -i ポートナンバー
nc -zv 対象ホスト名 ポート番号
```

### ネットワークマネージャー

```sh
nmcli d
nmcli device show ens160
```

### 名前解決

nslookupは非推奨、digが推奨。hostコマンドとdigコマンドはIPアドレスを入力してドメイン名を逆引きしたりも可能

- nslookup

```sh
nslookup
>google.com
```

- host

```sh
host google.com
```

- dig

```sh
dig google.com
```

- nc

```sh
# ボートの状態を確認。-vは冗長化
nc -zv 対象ホスト名 ポート番号
# サーバとして待受。-lはサーバーモードで待ち受け、-pはポート番号を指定
nc -l -p ポート番号
```

- traceroute

指定したホストまでの経路(ルーティング情報)を表示する

```sh
traceroute google.com
# 出力結果に 「*」 と表示されたら、その経路で問題が発生している可能性がある
```

- route

ルーティングテーブルの参照や変更が可能（廃止予定でipコマンドへ移行が進んでいる)

```sh
# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         ip-192-168-x-x. 0.0.0.0         UG    0      0        0 eth0
instance-data.a 0.0.0.0         255.255.255.255 UH    0      0        0 eth0
192.168.x.x     0.0.0.0         255.255.255.0   U     0      0        0 eth0
```

### nice値

```sh
# nice値確認
ps -l
ps -c stress -o pid,ppid,args,ni,psr,wchan
top 

# nice値を変更して実行
nice -n 5 python sample.py
nice -20 [command]
```

### 稼働時間

```sh
uptime
>0:24  up 12 days,  1:36, 3 users, load averages: 1.69 2.10 2.02
```

- pmset

Macで使用できる電源管理や省エネルギーモードに関するカスタマイズを行うコマンド

```sh
# 電源管理設定を表示
pmset -g
# スリープを無効にする
pmset sleep 0
# スリープになるまでを30分とする
pmset sleep 30
# ディスプレイオフになるまでを30分とする
pmset displaysleep 15
```

### ファイル系

#### ファイルタイプ確認

```sh
file -s 対象ファイル
```

#### ファイル上限数確認

```sh
ulimit -n ディレクトリ名
```

#### md5sum

```sh
md5sum ファイル名
```

### サービス

- 定義されているサービス一覧と自動起動の状態

```sh
systemctl list-unit-files --type=service
```

- 失敗しているサービスの確認

```sh
systemctl list-units --state=failed
```

- journalctl

```sh
# 基本的な使い方. -u で対象サービスを指定してログを確認
journalctl -u php-fpm
# -n 表示する行数を指定する
journalctl -n 5 -u php-fpm
```

---

## パフォーマンス測定

- [vmstat](#vmstat)
- [netstat](#netstat)
- [sar](#sar)
- [CPU](#cpu)
- [メモリ](#メモリ)
- [ディスク](#ディスク)

### vmstat

vmstat 秒サイクル 表示回数

- vmstatコマンド例 vmstat 秒サイクル 表示回数
- 最初の１例目は当てにならない(サーバ起動時からの統計値となり、リアルタイム性がない)

```sh
vmstat 2 7
```

### netstat

- network-staticsの略
- ネットワーク接続やルーティングの状況、ネットワークインターフェースの状態を表示する
- CentOS7, RHEL7では非推奨(代替 ss)。ただしssの不具合も報告されている
- Stateの内容
  - ESTABLISHED: 通信中
  - LISTENING: 待受中
  - TIME_WAIT: 終了待ち
  - CLOSE_WAIT: 通信終了

```sh
# 待機中も含めて全ポートを表示
netstat -a
# ホストやユーザーの名前解決を行わずにアドレス表示する
netstat -n
# リスニング中ソケットを表示
netstat -l
```

### ss

```sh
# 待機中も含めて全ポートを表示
ss -a
# ホストやユーザーの名前解決を行わずにアドレス表示する
ss -n | grep ポートナンバー
# リスニング中ソケットを表示
ss -l
```

### lsof

```sh
# 全てのプロセスについて表示する
lsof
# プロセス指定
lsof -p プロセス番号
# ネットワーク接続の状態を表示
lsof -i
# ポート指定
lsof -i:ポート番号
lsof -i:ポート番号,ポート番号 # 複数指定
# 特定のファイルを開いているプロセスを特定
lsof ファイルパス
# ユーザーを指定
lsof -u ユーザー名
```

### sar

sar (System Activity Report)

- CPU、Memoryメモリ、ネットワーク、I/Oなどの付加状況を表示する
- 過去に遡って確認可能
- 低負荷のため本番環境でもパフォーマンス影響は少ない

#### インストール

```sh
yum install sysstat
```

#### コマンド例

```sh
# 基本
sar 5(インターバル秒数) 3(実行回数)
# CPU使用率
sar -u
# コアごとのCPU使用率表示
sar -P ALL
# メモリ使用率
sar -r
# ネットワーク負荷
sar -n DEV
# ディスクI/O
sar -d
# I/Oと転送率の状況
sar -b
# ロードアベレージ
sar -q
# ファイル保管場所指定
sar -r -f /var/log/sa/sa日付名
# 時間指定で情報を取得 -s 開始時刻  -e 終了時刻
sar -r -f /var/log/sa/sa日付名 -s "20:00:00" -e "23:00:00"
```

### CPU

#### top(CPU、メモリ、プロセスの状態表示)

- load-averageはディスクIO待ちのプロセス数を表す。左から1分、5分、15分平均で表示される。
- CPU表示 us->ユーザー空間プロセスの時間割合 sy->カーネル空間プロセスの時間割合 id->idle状態の時間割合
- メモリ表示
- total = used + buff/cache + free
- buff/cache = 解放不可の領域と解放可能の領域があり、よくfreeの領域が減ることと連動してbuff/cacheが増える
- Avail Mem = free + buff/cache(解放可能領域)

```sh
# リアルタイムにプロセスの状態表示が可能
top

q 終了
? or help ヘルプ表示
P CPU率でソート
M メモリ使用率でソート
T プロセス稼働時間の長さ順でソート


top - ①18:23:27 ②up 1 day, 21:32,  ③0 users,  ④load average: 0.00, 0.00, 0.00
# ①現在時刻 ②起動時間, ③ログインユーザー数, ④ロードアベレージ(1分、5分、15分平均)スレッド数(4つなら4)以上の数字だとCPUが忙しすぎるという意味になる
⑤Tasks:   2 total,   ⑥1 running,   ⑦1 sleeping,   ⑧0 stopped,   ⑨0 zombie
# ⑤合計プロセス数　⑥稼働プロセス数　⑦休止プロセス数　⑧停止プロセス数　⑨ゾンビプロセス数
%Cpu(s):  0.7 us,  0.2 sy,  0.0 ni, 99.1 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
# waはディスクがどれだけ忙しいかを表示。0が望ましい。0.1でもずっとその表示が続いていると忙しいという意味
KiB Mem :  4026796 total,   320084 free,   482340 used,  3224372 buff/cache
KiB Swap:  1048572 total,  1039796 free,     8776 used.  3036384 avail Mem
# メモリはavail Mem欄をよく確認
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
    1 ec2-user  20   0    5276   3112   2696 S   0.0  0.1   0:00.13 bash
   45 ec2-user  20   0   10024   3732   3232 R   0.0  0.1   0:00.01 top
# %CPUでどのプロセスが何％CPUを使用しているか確認可能。ただし4つのスレッドがあれば許容量は400%になる
```

#### htop

topのグラフ表示機能が付属した機能がある。どのスレッドが忙しいかなど視覚的に確認可能

#### lscpu

CPUのコア数など確認可能

### メモリ

```sh
free 
free -m       # メガバイト
free -g       # ギガバイト
free -h       # 適切な形態に併せて表示
free --total  # 空き領域の合計も併せて表示する

$ free -m
              total        used        free      shared  buff/cache   available
Mem:           3932         471         310         316        3150        2964
Swap:          1023           8        1015
# availableの表示を要確認
# buff/cache - 使用していないメモリをファイルの読み書きに当てたりする数字。この数字は高くてもそこまで気にしなくてもいい
# free以上のメモリが必要になるときはbuff/cacheもメモリが解放されて使用されるようになる
```

### ディスク

#### df

```sh
df
df -kP      # ブロックサイズが1kで表示 P=POSIX出力形式
df -i       # i=inode情報を表示する
df -h       # サイズに応じて読みやすい単位で表示する
df -T       # ファイルシステムタイプを含めて表示する
df --total  # 空き領域の合計も併せて表示する
```

#### du

```sh
# 各ディレクトリやファイルごとに確認
du -sh ./*
du -h -d 1 . # カレントディレクトリ下の角ファイルやディレクトリのサイズを確認
```

#### lsblk

```sh
# HDD、SSD、USBフラッシュドライブ、パーティションなどのブロック単位(ブロックデバイス)の情報を表示する
$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme0n1       １２３:0    0  60G  0 disk
├─nvme0n1p1   １２３:1    0  60G  0 part /
└─nvme0n1p128 １２３:2    0   1M  0 part

# メジャー番号 (MAJ) はカーネル内でデバイスドライバを特定するための番号
# マイナー番号 (MIN) は特定のデバイスドライバ内で識別されるデバイスのインスタンスを表す番号
```

#### パーティション拡張

```sh
# パーティションのサイズを拡張する
sudo growpart /dev/nvme0n1(変更を加えたいディスクを指定する) 1(変更を加えたいパーティションを指定する部分、1は最初のパーティション（通常、ルートパーティション）を指す)
```

#### ファイルシステム拡張

```sh
# xfsファイルシステムのサイズを拡張する
sudo xfs_growfs -d(デバイスでサポートされる最大サイズまで増加させる) /(拡張対象のファイルシステム)

# ext(2/3/4)ファイルシステムのサイズを拡張する
sudo resize2fs /dev/nvme0n1p1(拡張対象のファイルシステム)
```

## [CPUアーキテクチャ](https://developer.a-blogcms.jp/blog/custom/docker-arm64.html)

| アーキテクチャ | 概要 | OS例 |
| - | - | - |
| amd64 | 「AMD社」が発表したx86アーキテクチャを64bitに拡張したもの | Windows、Linux、Intel Mac |
| intel64 | 「インテル社」が発表したx86アーキテクチャを64bitに拡張したもの | Windows、Linux、Intel Mac |
| x86-64 | amd64とIntel64を含んだ総称 | Windows、Linux、Intel Mac |
| arm64/aarch64 | スマホやタブレットなどで多く利用されているARMアーキテクチャを64bitに拡張したもの | iOS、M1/M2 Mac、ARM版Windows |
|  |  |  |

---
