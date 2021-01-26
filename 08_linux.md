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
