## CentOS7へmysqlインストール

* MariaDBがインストールされている場合はこれからインストールするMySQLと競合を起こさないように削除

```
sudo yum remove mariadb-libs
sudo rm -rf /var/lib/mysql
```

* MySQL公式のyumリポジトリの追加

```
sudo rpm -ivh http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
```

* MySQLのインストール

```
sudo yum install mysql-community-server
```

* バージョンを確認

```
$ mysqld --version
mysqld  Ver 5.7.14 for Linux on x86_64 (MySQL Community Server (GPL))
```

* MySQLの起動

```
sudo systemctl start mysqld.service
```

* 自動起動の設定

```
sudo systemctl enable mysqld.service
```

* MySQLの初期パスワード確認
※`MySQL5.7`では、初回起動時に初期パスワードが生成されるので、ログファイルから生成されたパスワードを確認

```
$ sudo cat /var/log/mysqld.log | grep password
2016-09-01T13:09:03.337119Z 1 [Note] A temporary password is generated for root@localhost: xxxx!XXX;XXXX
```

* MySQLのセキュリティ設定
mysql_secure_installationコマンドを実行して、MySQLのセキュリティ設定を行う

```
$ mysql_secure_installation

Securing the MySQL server deployment.

Enter password for user root: # ログファイルから取得した初期パスワードを入力します

The existing password for the user account root has expired. Please set a new password.

New password: # root ユーザの新規パスワードを入力します

Re-enter new password: # 確認用にもう一度入力します
The 'validate_password' plugin is installed on the server.
The subsequent steps will run with the existing configuration
of the plugin.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : y

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y # 匿名ユーザーアカウントを削除
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y # ローカルホスト以外からアクセス可能な root アカウントを削除
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y # test データベースの削除
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

* 設定変更
※MySQL5.7からパスワードの有効期限がデフォルトで360日になり、360日を経過するとパスワードの変更を促されてMySQLにログインできなくなる。
default_password_lifetime の値を `0` に変更して、この有効期限の設定を無効にする
また、デフォルトの文字コードも UTF-8 に変更

```
/etc/my.cnf
[mysqld]
.
.
.
character-set-server = utf8
default_password_lifetime = 0
```

* MySQLを再起動させ、設定内容を反映

```
sudo systemctl restart mysqld.service
```

