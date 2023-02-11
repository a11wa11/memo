# ネットワーク

- [OSI](#osi)
- [HTTP](#http)
- [システム情報](#システム情報)
- [パフォーマンス測定](#パフォーマンス測定)

## OSI

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
cat /etc/hostname  
hostnamectl status 
```

- ホスト名変更

```sh
hostnamectl set-hostname ホストネーム名
```

### タイムゾーン

- タイムゾーンを確認

```sh
timedatectl status
cat /etc/localtime
```

- タイムゾーン変更

```sh
cp /etc/localtime /etc/localtime.org # バックアップ
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

### ロケール

```sh
# ロケールを確認
locale
localectl

# ロケール変更
export LC_ALL=ja_JP.UTF-8
```

### IPアドレス

#### ip

```sh
# IPアドレスの確認
ip a
ip addr show
ifconfig # 古くて今はあまり推奨されていない

# 下記のファイルのIPADDRの変更などでIPアドレスを変更可能
/etc/sysconfig/network-scripts/ifcfg*
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

```sh
# 定義されているサービス一覧と自動起動の状態
systemctl list-unit-files --type=service
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
# CPU使用率
sar -P ALL
# メモリ使用率
sar -r
# ネットワーク負荷
sar -n DEV
# ディスクI/O
sar -d -p
# I/Oと転送率の状況
sar -b
# ファイル保管場所指定
sar -r -f /var/log/sa/sa日付名
# 時間指定で情報を取得 -s 開始時刻  -e 終了時刻
sar -r -f /var/log/sa/sa日付名 -s "20:00:00" -e "23:00:00"
```

### CPU

top(プロセスの状態表示)

- load-averageはディスクIO待ちのプロセス数を表す。左から1分、5分、15分平均で表示される
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
```

### メモリ

```sh
free 
free -m       # メガバイト
free -g       # ギガバイト
free -h       # 適切な形態に併せて表示
free --total  # 空き領域の合計も併せて表示する

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
