# linux

## apt

```sh
# インストール可能なバージョンを確認
apt-cache policy パッケージ名
apt-cache showpkg パッケージ名

# バージョンを指定してインストール
apt-get install パッケージ名=バージョン

# mysqlclientのインストール
apt-get install -y libmysqlclient-dev
```

## yum

```sh
# どのバージョンが存在するかを検索
yum search --showduplicates search パッケージ名

# rpmパッケージのURLを直接指定してインストール
yum install http://mirror.centos.orgXXXXX.noarch.rpm

# リポジトリを確認
yum repolist

# 有効なリポジトリを確認
yum repoinfo

# リポジトリをアップデート
yum update epel

# amazon-linuxでのEPEL追加
amazon-linux-extras install -y epel
```

### RPM

* i -> install
* U -> update
* e -> erase(uninstall)
* v verbose
* h 進行状況を表す

`-U`はインストールの意味も含まれる。パッケージがなければインストール、パッケージが古ければアップデートという挙動になっている。  
カーネル以外のパッケージは`-U`でインストール、カーネル関連は`-i`でインストールすることが推奨

```sh
rpm -ivh パッケージ名.rpm
rpm -Uvh パッケージ名.rpm
rpm -evh パッケージ名.rpm
```

## log

* messages
  * `/var/log`ディレクトリに格納されるログファイルの１つ

```sh
/var/
  └──log/
      ├───messages
      ├───cron
      ├───maillog
      ├───secure
```

* syslog
  * システムログを転送するための標準プロトコル
    * 出力元や優先度に従って分類を行い、特定の出力先に送信する
    * TCPの場合、514ポートを使ってsyslogサーバに送信される
  * linuxで使用される
  * rsyslogというsyslogの改良版もある
  * ファシリティ
    * ログの種別、もしくは出力先とも言える
    * kern（カーネル）,daemon（デーモン）,user（ユーザープログラム）, auth（認証プログラム）など
    * `*`で全ての↑ファシリティを指定可能
  * プライオリティ
    * メッセージの優先度のこと
    * 指定したプライオリティ以上のログが出力される
  * 以下の例はcronを除いた全てのファシリティでinfo以上のメッセージを出力という意味

```sh
cat /etc/rsyslog

*.info;cron.none;         /var/log/messages
```

* logrotate
  * 取得頻度 -> daily, weekly, monthly
  * ローテーション回数 -> `rotate 3`
  * 新規ファイル作成 -> create, nocreate
  * 拡張子 -> dateext(日付拡張子をローテートファイルにつける)

* ファイルシステム
  * APFS Mac
  * FAT Windows
  * ext4 CentOS6
  * xfs CentOS7 -> EBSではxfsでフォーマットが推奨されている

* [任意のログをlogrotateを使って管理する](https://qiita.com/Esfahan/items/a8058f1eb593170855a1)
* [logrotateの簡単設定](https://qiita.com/Esfahan/items/a8058f1eb593170855a1)
* [【logrotate】の仕組みと書き方, オプション一覧, 設定反映と再起動について](https://milestone-of-se.nesuke.com/sv-basic/linux-basic/logrotate/)


## 初期構築テンプレート

* CentOS7へデスクトップツールをインストール

```sh
#!/bin/bash
# 1. first setup
yum update -y
localectl set-locale LANG=ja_JP.utf8
timedatectl set-timezone Asia/Tokyo
yum install -y tree vim wget openssl-devel epel-release
yum groupinstall -y "Development Tools"
cd /root
# 2. python setup
wget https://www.python.org/ftp/python/3.5.9/Python-3.5.9.tar.xz
tar -Jxvf Python-3.5.9.tar.xz
Python-3.5.9/configure --prefix=/usr/local/python
make
make install
rm -rf /root/*
ln -s /usr/local/python/bin/python3.5 /usr/local/bin/python
ln -s /usr/local/python/bin/pip3.5 /usr/local/bin/pip
echo "export PATH=$PATH:/usr/local/python/bin" >> ~/.bashrc
# 3. desktop setup
yum install -y xrdp tigervnc-server
yum groupinstall -y "GNOME Desktop"
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bk
cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bk
sed -i s/enabled=1/enabled=0/g /etc/yum.repos.d/epel.repo
sed -i s/"PasswordAuthentication no"/"PasswordAuthentication yes"/ /etc/ssh/sshd_config
sed -i s/max_bpp=32/max_bpp=16/ /etc/xrdp/xrdp.ini
systemctl start firewalld
firewall-cmd --permanent --zone=public --add-port=3389/tcp
firewall-cmd --permanent --zone=public --add-port=5000/tcp
firewall-cmd --reload
systemctl set-default multi-user.target
systemctl stop packagekit
systemctl mask packagekit
systemctl start xrdp
systemctl enable xrdp
# 4. mysql setup
yum install -y mysql

# 5. nginx setup
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
