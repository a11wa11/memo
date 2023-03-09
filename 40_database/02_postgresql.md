# postgresql

## コマンド

* 接続

```psql
psql -h ホスト名 -U ユーザー名 -p 5432 -d データベース名
```
* 確認系

```psql
# データベース
\l (show databases;)
# テーブル
\d (show tables;)
# タイムゾーン
show timezone;
# 現在時間
select now();
```

* dump

```psql
pg_dump -h ホスト名 -U ユーザー名 -p 5432 -d データベース名 > backup_file名
```
* リストア

```psql
pg_restore -C -d データベース名 バックアップファイル名
psql -h ホスト名 -U ユーザー名 -d データベース名 -f バックアップファイル名
```

[mysqlとpsql](https://qiita.com/aosho235/items/c657e2fcd15fa0647471)

