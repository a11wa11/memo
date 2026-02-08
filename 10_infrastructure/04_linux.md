# linux

- [linux](#linux)
  - [ubuntu](#ubuntu)
    - [apt](#apt)
  - [debian](#debian)
  - [redhat](#redhat)
    - [yum](#yum)
    - [dnf](#dnf)
    - [RPM](#rpm)
  - [amazon-linux](#amazon-linux)
  - [alpine](#alpine)
  - [代表的なファイルシステムに格納されているもの](#代表的なファイルシステムに格納されているもの)
    - [log](#log)
    - [syslog](#syslog)
    - [logrotate](#logrotate)
    - [RAID](#raid)
    - [マウント関連](#マウント関連)
      - [マウント](#マウント)
      - [アンマウント](#アンマウント)
      - [fstab](#fstab)
    - [glibc](#glibc)
  - [初期構築テンプレート](#初期構築テンプレート)

## ubuntu

### apt

- debianでも使用する

```sh
# インストール可能なバージョンを確認
apt list -a パッケージ名
apt-cache policy パッケージ名
apt-cache showpkg パッケージ名

# バージョンを指定してインストール
apt-get install パッケージ名=バージョン

# mysqlclientのインストール
apt-get install -y libmysqlclient-dev

# インストール済パッケージ一覧
apt list
apt list --installed

# パッケージ詳細確認
apt show パッケージ名

# リポジトリの最新情報更新
apt update
# インストール済パッケージ更新
apt upgrade

# 他のパッケージが依存しなくなった不要なパッケージを削除
apt autoremove
```

- apt-getよく使うパッケージやコマンド

```sh
# psコマンドやfreeコマンド
apt-get install procps

# キャッシュクリア
apt-get clean
# or
apt-get clean && rm -rf /var/lib/apt/lists/*
```

## debian

- 古いリリースのパッケージを引き続き使用できるように変更
  - DebianベースのLinuxシステムで、/etc/apt/sources.listファイルを更新
    - 古いリポジトリをarchive.debian.orgに切り替えた後、パッケージ情報を更新する

```sh
# Update repository to use archive.debian.org and remove stretch-updates
sed -i 's|http://deb.debian.org/debian|http://archive.debian.org/debian|g' /etc/apt/sources.list                        # URLをarchive.debian.orgに変更
sed -i 's|http://security.debian.org/debian-security|http://archive.debian.org/debian-security|g' /etc/apt/sources.list # セキュリティリポジトリを変更
sed -i '/stretch-updates/d' /etc/apt/sources.list                                                                       # stretch-updatesを(このリポジトリを/etc/apt/sources.listに残したままだとAPTの更新時にエラーが発生し、システムのパッケージ管理がスムーズに行えなくなるため)削除
apt-get -o Acquire::Check-Valid-Until=false update                                                                      # APTのリポジトリの有効期限チェックを無効にしつつ、パッケージリストを更新する
```

## redhat

### yum

```sh
# パッケージのリストを表示する
yum list installed # インストール済パッケージ
yum list available # 利用可能なパッケージ
yum list updates   # アップデート可能なパッケージ
yum list extras    # 利用できないパッケージ

# パッケージのインストール
yum install パッケージ名
yum install パッケージ名-バージョン名(例 httpd-2.4.6-97 など)

# どのバージョンが存在するかを検索
yum search --showduplicates パッケージ名
yum search all --showduplicates パッケージ名

# パッケージ詳細を表示
yum info パッケージ名

# ファイル名からパッケージを検索する
yum provides パッケージ名

# rpmパッケージのURLを直接指定してインストール
yum install http://mirror.centos.orgXXXXX.noarch.rpm

# ローカルインストール
wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
yum localinstall mysql80-community-release-el7-7.noarch.rpm(ファイルパス)

# リポジトリを確認
yum repolist

# 有効なリポジトリを確認
yum repoinfo

# リポジトリ関連コマンド yum-config-manager

# リポジトリを有効にする。enabled=1になる
yum-config-manager --enable epel

# リポジトリを無効にする。enabled=0になる
yum-config-manager --disable epel

# リポジトリを追加
yum-config-manager --add-repo=https://example.com/repo

#　リポジトリの削除
yum-config-manager --remove epel

# リポジトリの優先度設定
yum-config-manager --save --setopt="epel.priority=10"

# リポジトリをアップデート
yum update epel

# 一時的にリポジトリを有効にして実行
yum --enablerepo=epel install ライブラリ名
yum --disablerepo=epel install,update,search ライブラリ名

# Google chrome & driver インストール
RUN yum install -y https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm --skip-broken
```

### dnf

```sh
# パッケージ検索
dnf search パッケージ名
# インストール可能か確認
dnf list available パッケージ名(正規表現も使える)
# パッケージ詳細を表示
dnf info パッケージ名
# システムに設定されているすべてのリポジトリの一覧を有効か無効かも含めて表示
dnf repolist all
```

### RPM

- i -> install
- U -> update
- e -> erase(uninstall)
- v verbose
- h 進行状況を表す
- q クエリーオプション。 `rpm -q パッケージ名`でインストールされているパッケージのバージョン情報を確認。`rpm -qa`なら全てのインストール済のrpmパッケージを一覧表示する

`-U`はインストールの意味も含まれる。パッケージがなければインストール、パッケージが古ければアップデートという挙動になっている。  
カーネル以外のパッケージは`-U`でインストール、カーネル関連は`-i`でインストールすることが推奨

```sh
rpm -ivh パッケージ名.rpm
rpm -Uvh パッケージ名.rpm
rpm -evh パッケージ名.rpm
```

- .rpmsaveとは？

RPMパッケージにおける“アップグレードは慎重に”という方針に基づいて作成されるバックアップファイルのようなもので、.rpmsaveというファイルを使って復元用の情報を保存させるようにしている

## amazon-linux

```sh
# amazon-linuxでのEPEL追加
amazon-linux-extras install -y epel

# amazon-linux-extras のリスト確認
amazon-linux-extras
amazon-linux-extras list # list省略可能
```

## alpine

```sh
# パッケージインストール
apk add パッケージ名
apk add --no-cache パッケージ名 # インストール後にキャッシュクリア

# アンインストール
apk del パッケージ名

# パッケージ検索
apk search パッケージ名

# パッケージ詳細
apk info パッケージ名

# リポジトリアップデート
apk update

# パッケージアップグレード
apk upgrade

# パッケージリスト
apk list --installed # インストール済
apk list --available # 利用可能
```

## 代表的なファイルシステムに格納されているもの

```sh
/dev         # ハーディディスク、DVD-ROMなどのデバイスファイル
/media       # DVD-ROMなどのリムーバブルメディアのマウントポイント
/mnt         # 一時的にマウントするファイルシステムのマウントポイント
/etc         # システム、コマンドなどの設定ファイル
/root        # rootユーザーのホームディレクトリ
/home        # rootユーザーのホームディレクトリ
/bin         # 一般ユーザーが実行可能なシステムコマンド(ls,cp,rm,mkdir,catなど)
/sbin        # rootユーザーが実行可能なシステムコマンド(rebootなど)
/lib         # 共有ライブラリやモジュール、/bin、/sbin にあるコマンドが利用するライブラリ
/usr         # ユーザーが共有するコマンド
 └──bin      # 一般ユーザーとrootユーザーが使用する基本コマンド(git,curlなど)
 └──sbin     # rootユーザーが使用する基本コマンド
 └──lib      # プログラムの共有ライブラリ
 └──local    # 個人で作成したコマンドやサードパーティ独自に追加したコマンドなど
 └──src      # linuxのカーネルソースなどのソースコード
/opt         # 追加パッケージや追加プログラム(google-chromeやeclipseなど)
/proc        # カーネル内部の情報にアクセスする
/tmp         # 一時ファイルが配置され、すべてのユーザーが読み書き可能
/boot        # 軌道に必要なカーベルイメージ
/var         # ログファイルなどの頻繁に書き込まれるファイル群
 └──cache    # 一時的なキャッシュファイル
 └──lock     # アプリケーションを制御するためのロックファイル
 └──log      # ログファイル
 └──run      # システムの状態を示すファイル
 └──spool    # 印刷待ちのデータ、予約されたジョブ
```

### log

```sh
/var/
  └──log/
      ├───messages
      ├───cron
      ├───maillog
      ├───secure
```

- last
  - `/var/log/wtmp`に保存されているユーザーのログインとログアウト、システムの再起動やシャットダウンなどの履歴を閲覧可能
- lastb
  - `/var/log/btmp`に保存されているユーザーのログイン失敗の履歴を閲覧可能。不正なアクセスの試みやパスワードのブルートフォース攻撃を検知するのに役立つ

### syslog

- システムログを転送するための標準プロトコル
  - 出力元や優先度に従って分類を行い、特定の出力先に送信する
  - TCPの場合、514ポートを使ってsyslogサーバに送信される
- linuxで使用される
- rsyslogというsyslogの改良版もある
- ファシリティ
  - ログの種別、もしくは出力先とも言える
  - kern（カーネル）,daemon（デーモン）,user（ユーザープログラム）, auth（認証プログラム）など
  - `*`で全ての↑ファシリティを指定可能
- プライオリティ
  - メッセージの優先度のこと
  - 指定したプライオリティ以上のログが出力される
- 以下の例はcronを除いた全てのファシリティでinfo以上のメッセージを出力という意味

```sh
cat /etc/rsyslog

*.info;cron.none;         /var/log/messages
```

### logrotate

ログローテートとは、ログファイルのサイズが大きくならないように、1日、または1週、または1か月といった期間でファイル名を変更し、設定次第では古いファイルを圧縮したり、もっと古いファイルを削除したりすることができる仕組みのこと。  
実体は`/usr/sbin/logrotate`というコマンドとcron.dailyの組合せであり、crondによってlogrotateコマンドが毎日実行されているため、デーモンとして起動されているわけではない

- 実行記録

一般的に`/var/lib/logrotate/logrotate.status`に前回の実行記録が保存されている。  
`/etc/cron.daily/logrotate`などのファイルを見ると`/usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf`と記載があり、`-s`オプションで指定しているのが実行記録が保存されているファイルパス。  
`-s`オプションで指定しなくても`/var/lib/logrotate/logrotate.status`がデフォルトファイルパスになっている

- 構成

```sh
/etc/
├── logrotate.conf # メイン設定ファイル
├── logrotate.d    # 各サービスごとの設定ファイル
│   ├── nginx
│   ├── syslog
│   ├── yum
```

- 各設定項目
  - 取得頻度 -> daily, weekly, monthly
  - ローテーション回数 -> `rotate 3`
  - 新規ファイル作成 -> create, nocreate
  - 拡張子 -> dateext(日付拡張子をローテートファイルにつける)
  - 設定例

```log
/var/log/nginx/*log {
daily                    # ローテーション頻度 この例は毎日
weekly                   # ローテーション頻度 この例は毎週
monthly                  # ローテーション頻度 この例は毎月
rotate 30                # ログファイルを何世代分保持するか この例は30世代
missingok                # ログファイルが存在しない場合でもエラーを出さずに処理を続行
notifempty               # ログファイルが空でない場合にのみローテーション実行
compress                 # ローテーションされたログファイルを圧縮。通常はgzip形式で圧縮
delaycompress            # ローテーションされた直後のログファイルは圧縮せず、次のローテーション時に圧縮
sharedscripts            # 複数のログファイルがローテーションされる場合でもスクリプトは一度だけ実行
create 0644 nginx nginx  # 新しいログファイルを作成する際のパーミッションと所有者を指定。この例は0644、所有者とユーザーはnginxグループ
su root root             # ログローテーションの実行ユーザーとグループを設定 この例はroot
postrotate               # ログのローテーション後に実行するスクリプトの開始を示す
    nginx -s reopen      # 指示したいスクリプト内容
endscript                # postrotateスクリプトの終了
copytruncate             # ログファイルをコピーして新しいファイルに保存し、元のファイルを空にする
size 2M                  # ログファイルサイズが一定サイズ以上の場合にローテーションする この例は2MB
olddir /var/log/cron_old # ローテーションされたログファイルを保存するディレクトリを指定
```

- logrotateコマンド

```sh
# 実行ログの確認
less /var/lib/logrotate.status
# 全テスト -dはdebugでdry-run実行。-vは詳細表示
logrotate -dv /etc/logrotate.conf
# 個別テスト
logrotate -dv /etc/logrotate.d/nginx
# 手動強制実行。-fは--forceで
logrotate -fv /etc/logrotate.conf
```

- 参考
  - [任意のログをlogrotateを使って管理する](https://qiita.com/Esfahan/items/a8058f1eb593170855a1)
  - [【logrotate】の仕組みと書き方, オプション一覧, 設定反映と再起動について](https://milestone-of-se.nesuke.com/sv-basic/linux-basic/logrotate/)
  - [logrotateの設定](https://humo-life.net/blog/centos/187/)
  - [logrotateの全オプション解説](https://hackers-high.com/linux/man-jp-logrotate/)
  
- ファイルシステム
  - APFS Mac
  - FAT Windows
  - ext4 CentOS6
  - xfs CentOS7 -> EBSではxfsでフォーマットが推奨されている

### RAID

- 概要
  - 複数のディスクをまとめて1台のディスクとして管理する技術
  - ディスクへのデータの割り振り方やデータの冗長化の方法によっていくつかのレベルがある
    - RAID0(ストライピング)
      - データをブロック単位に分割し、複数のディスクに分散して配置する
      - メリットは読み込み、書き込みが高速
      - デメリットは冗長性がなく、復旧不可能
    - RAID1(ミラーリング)
      - 同じデータを複製し、2つのディスクに書き込む方式
      - メリットは片方のディスクが破損してもファイルが残るため、信頼性が高い
      - デメリットは処理速が向上せず、コストが倍になる
    - RAID5
      - RAID0と同じく、複数のディスクにデータを分散して配置すると同時にパリティデータを計算・生成してディスクに書き込む方式
        - パリティとはディスクに障害が発生した場合にデータを復元するための符号
        - どれか1台のディスクが故障してもその他のディスクのデータとパリティ情報を使用しデータを完全に回復可能
      - メリットはディスクの利用効率が高い。読み込み性能が向上すること
      - デメリットは2台以上の破損の場合には復旧不可能。パリティ算出の付随処理によって書き込み性能が向上しない
    - RAID6
      - パリティを２重で生成し、異なるディスクに記録する方式
      - メリットはRAID5より耐障害性が向上
      - デメリットはRAID5に比して書き込み性能や容量効率が低い

### マウント関連

#### マウント

- Linuxでは接続したストレージデバイスを利用するにはLinuxシステムに**デバイスを認識させる**必要がある。認識させなければ中身にアクセスできず、認識させる作業をマウントと呼ぶ
- 接続したストレージデバイスをマウントする際に指定するディレクトリのことをマウントポイントという
- mount(umount)コマンドは管理者でのみ実行可能

```sh
mount /dev/sdb1(ストレージデバイス) /mnt(マウント先)
ls /mnt # 中身が見れる様になる
sample1.txt sample2.txt  
mount /dev/sdb1(ストレージデバイス) /mnt(マウント先)
```

#### アンマウント

- ストレージデバイスを切り離す作業をアンマウントと言う

```sh
umount /mnt(マウント先)
ls /mnt # 中身が見れない
```

#### fstab

- `/etc/fstab`　について
  - ディスクパーティションやブロックデバイスなどをどのようにしてファイルシステムにマウントするか記述されている設定ファイル
  - mountコマンドだけでは再起動したりするとマウントも外れてしまうため、永続的にマウントするためには `/etc/fstab` に記述しなければならない
  - `/etc/fstab`の内容を変更後は設定を反映させるために`mount -a`コマンド、もしくはOS再起動する必要がある

```sh
cat /etc/fstab

UUID=XXXX / xfs defaults.noatime 1 1

# ↑ 計６つのステータスについてスペースかタブ区切りで記述される
①file-system(デバイス名でも記述可能) ②ディレクトリ ③type ④option ⑤dump ⑥pass
```

- UUIDとは機器を管理・識別するためのユニークなIDで`blkid`コマンドでUUIDを調べることができる
  - fstabにUUIDで記述すると構成が変更された際にデバイス名が変更されても指定したUUIDのデバイスを選択する
  - デバイス名で記述していた場合、デバイス名が変更されると正しくマウントされずに起動できないケースがある

- type
  - マウントされるパーティションやストレージデバイスのファイルシステムタイプのこと
  - `xfs` や `ext2,3,4`、`swap`などがある

- option
  - マウント時の読み書き可能かなどのオプションを指定可能
  - defaultsのオプションがよく使われる

- dump
  - dump(バックアップ)が必要かどうか。
  - １が必要、０、または記述なしが不要
  - `dump`コマンドはex2,3,4のファイルシステムをバックアップするために指定場所へコピーするコマンド
  - xfsのバックアップは`xfsdump`コマンドを使用する

- pass
  - `fsck`がブート時にファイルシステムのチェックを実行する順序を決定するために参照する
  - ルートファイルシステムは１と記述する必要がある
  - 0の場合、`fsck`によるチェック不要とみなされる
  - `fsck`コマンドではファイルシステムを検査・修復するコマンド

### glibc

GNUシステム用の標準Cライブラリ。GNU/Linuxシステム上のほとんどのプログラムで使用されている

- [glibcの更新方法](https://tutorials.tinkink.net/ja/linux/how-to-update-glibc.html)

```sh
# インストール
mkdir ~/tmp/glibc
cd ~/tmp/glibc
wget --no-check-certificate https://ftp.gnu.org/gnu/glibc/glibc-2.28.tar.gz
tar -xvf glibc-2.28.tar.gz
cd glibc-2.28
mkdir build && cd build
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
make&&make install
# 確認
strings /lib64/libc.so.6 | grep GLIBC_
```

### swap

swapとははRAMが足りなくなったときに、ディスクを一時的なメモリ置き場として使う仕組み。  
メモリ不足(OOM)でプロセスが殺されるのを防いだり、小さい仮想マシン(1GBなど)で安定性をあげるためにswapファイルを設定する

- swapファイルの作成手順(EC2)

```sh
# sudo権限で実行
##  if=input file of=output file bs=block size count=ブロック数
dd if=/dev/zero of=/swapfile bs=1G count=4 # 4GBのファイルを作る(中身はゼロ埋め)
chmod 600 /swapfile                        # ファイルの権限を設定
mkswap /swapfile                           # swapファイルを作成
swapon /swapfile                           # swapファイルを有効化
echo '/swapfile none swap sw 0 0' | tee -a /etc/fstab # 永続化

# 確認
swapon --show
free -h
```
