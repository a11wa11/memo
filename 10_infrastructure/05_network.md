# ネットワーク

- [OSI](#osi)
- [HTTP](#http)
- [システム情報](#システム情報)
- [パフォーマンス測定](#パフォーマンス測定)

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
```

- ホスト名変更

```sh
# 以下どちらでもOK
hostnamectl set-hostname ホストネーム名
hostname 'ホストネーム名'
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

# ロケール変更パターン1
export LC_ALL=ja_JP.UTF-8
# ロケール変更パターン2
echo "LANG=ja_JP.UTF-8" > /etc/sysconfig/i18n
# ロケール変更パターン3
localectl set-locale LANG=ja_JP.UTF-8
```

### IPアドレス

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

`/etc/sysconfig/network` (RedHat系OS)でネットワークに接続する際に必要とされる情報が記述されている

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

#### ネットワーク設定を反映

```sh
systemctl restart network
```

### ポート

```sh
netstat -an | grep ポートナンバー
ss | grep ポートナンバー
```

### ネットワークマネージャー

```sh
nmcli d
nmcli device show ens160
```

### 名前解決

```sh
nslookup
>google.com
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

- ネットワーク接続やルーティングの状況、ネットワークインターフェースの状態を表示する
- CentOS7, RHEL7では非推奨(代替 ss)。ただしssの不具合も報告されている

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
df --total  # 空き領域の合計も併せて表示する
```

#### du

```sh
# 各ディレクトリやファイルごとに確認
du -sh ./*
du -h -d 1 . # カレントディレクトリ下の角ファイルやディレクトリのサイズを確認

# list block devices
lsblk
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
