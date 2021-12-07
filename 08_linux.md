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
