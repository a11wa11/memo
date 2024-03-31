# postgresql

- [postgresql](#postgresql)
  - [コマンド](#コマンド)
    - [接続](#接続)
    - [確認系](#確認系)
    - [便利系](#便利系)
    - [調査](#調査)
    - [dump](#dump)
    - [リストア](#リストア)
    - [メンテナンス・パフォーマンス改善](#メンテナンスパフォーマンス改善)
    - [論理レプリケーション](#論理レプリケーション)
  - [pgbenchを使って測定](#pgbenchを使って測定)

## コマンド

### 接続

```sh
-- 基本
psql -h ホスト名 -U ユーザー名 -p 5432 -d データベース名

-- ファイル実行(-aでコメントも出力する)
psql -a -h ホスト名 -U ユーザー名 -p 5432 -d データベース名 < ファイル名.sql

-- データベース切替
\c データベース名
```

### 確認系

```sql
-- データベース
\l (show databases;)

-- テーブル
\d (show tables;)

-- テーブルのカラム名
\d+ テーブル名;

-- ユーザー権限
\du

-- ユーザー一覧
select * from pg_user;

-- タイムゾーン
show timezone;

-- 現在時間
select now();

-- バージョン
select version();
```

### 便利系

```sql
-- 見やすく縦表示の切替
\x
select * from テーブル名;

-- ページャーをlessに変更
--- 表示中に-N と押下すると最下部に「Constantly display line numbers (press RETURN)」と表示されるのでRETURNキー押下で行番号表示が可能。再度で行番号表示消去
\setenv PAGER less 

-- pagerのON/OFF切替
\pset pager

-- 実行時間を表示する
\timing

-- バックグラウンド実行
SELECT * FROM my_table \gexec

-- タイムアウト値変更
set statement_timeout = '10min';  #10分
set statement_timeout = '300s';   #300秒(5分)

-- メタコマンド シェルなど外部コマンドをpsql接続中に実施する
\! ls -l
```

### 調査

- pg_stat_activityビューに含まれる主な列は以下の通り

| 項目名 | 説明 |
|-|-|
| datid | データベースのOID（識別子） |
| datname | データベース名 |
| pid | プロセスID（セッションID） |
| usename | 接続ユーザー名 |
| client_addr | クライアントのIPアドレス |
| client_hostname | クライアントのホスト名 |
| query | 実行中のクエリ |
| state | セッションの状態（例 active, idle, idle in transactionなど） |
| wait_event_type | セッションが待機しているイベントの種類 |
| wait_event | セッションが待機している具体的なイベント |
| backend_start | バックエンドプロセスの開始時刻 |
| backend_type | バックエンドプロセスのタイプ（例: client backend, autovacuum launcherなど） |

```sql
-- 現在実行中のセッション（プロセス）に関する情報を取得
SELECT * FROM pg_stat_activity;

-- 準備済みトランザクション（Prepared Transaction）の数をカウントする
SELECT count(*) FROM pg_catalog.pg_prepared_xacts;

-- インストール可能な拡張機能を確認する
SELECT * FROM pg_available_extensions;

-- 現在のデータベースにインストールされているすべての拡張機能の情報を取得
SELECT * FROM pg_extension;

-- タイムアウト値確認
show statement_timeout;

-- 指定テーブルのカラム確認
select * from information_schema.columns where table_name='テーブル名' order by ordinal_position;

-- 使用ディスク容量を確認
SELECT pg_size_pretty(pg_database_size('データベース名'));
```

- [explainコマンド](https://postgresweb.com/post-4047)で調査

 ```sql
-- 推定された実行計画を表示する
explain select * from テーブル名;

-- 実際に実行した結果を表示する
explain analyze select * from テーブル名; 
```

### dump

```sh
# データベース全体dump
pg_dump -h ホスト名 -U ユーザー名 -p 5432 -d データベース名 > backup_file名
# テーブル指定(複数)
pg_dump -h ホスト名 -U ユーザー名 -p 5432 -d データベース名 -t テーブル1 -t テーブル2 -t テーブル3 > backup_file名
# バイナリ形式でdump保存 -F=ファイル形式 c=ファイル形式をPostgresqlのバイナリ形式
pg_dump -Fc -f ファイル名.dump -h ホスト名 -d データベース名 -U ユーザー名
```

### リストア

pg_restoreでバイナリファイル形式のバックファイルからのリストアの方が確実

```sh
pg_restore -h ホスト名 -C -d データベース名 バックアップファイル名
psql -h ホスト名 -U ユーザー名 -d データベース名 -f バックアップファイル名

# 特定のテーブルのみを指定する場合
pg_restore -h ホスト名 -C -d データベース名 -t テーブル名 バックアップファイル名
psql -h ホスト名 -U ユーザー名 -d データベース名 -t テーブル名1,テーブル名2,テーブル名3 -f バックアップファイル名
```

### メンテナンス・パフォーマンス改善

```sql
/*
データベース内のテーブルから削除された行や更新された行によって生じたスペースを回収し、データベースのスペースを最適化する
PostgreSQLはデータを消しても実際には消えてはなく、削除フラグがついていて見えなくなっているだけの状態になっている。この削除データは定期的にきれいにする必要があり、この処理をVACUUMという
*/
VACUUM;

-- ANALYZEはテーブル内のデータの分布や統計情報を収集し、クエリプランナーが適切なインデックスや結合方法を選択するための情報を提供する
ANALYZE;

-- VACUUMとANALYZEは下記のように同時実行可能
vacuum analyze;
```

### 論理レプリケーション

論理レプリケーションスロットとはデータベース間でデータの受け渡しを行うための仕組み。
送信側のデータベース（プライマリ）は、特別なスロットを作成し、データをそのスロットに格納する。
受信側のデータベース（レプリカ）は、定期的にそのスロットからデータを取得して自身のデータベースに反映させる。
データの複製元をパブリッシャー、複製先をサブスクライバーと呼ぶ（PUB/SUBモデルをベースにしている）

```sql
-- 既存の論理レプリケーションスロットを確認
select * from pg_replication_slots;
```

## [pgbench](https://www.postgresql.org/docs/current/pgbench.html)を使って測定

インストール

```sh
# amazonlinux2
sudo amazon-linux-extras install postgresql11
sudo yum install postgresql-contrib
```

初期化

```sh
# 基本
pgbench -i -h ホスト名 -p ポート番号 -U ユーザー名 -d データベース名
# スケーリングファクター数を調整
pgbench -i -h ホスト名 -p ポート番号 -U ユーザー名 -d データベース名 -s 2(スケーリングファクター数。デフォルトは1)
```

確認

```sh
# 基本
pgbench -h ホスト名 -U ユーザー名 -p ポート番号 -d データベース名 -T 300(テストしたい期間秒数) -c 10(想定クライアント数)

# スケーリングファクターを調整
pgbench -h ホスト名 -U ユーザー名 -p ポート番号 -d データベース名 -T 300(テストしたい期間秒数) -c 10(想定クライアント数) -s 10(スケーリングファクター)

# スレッド数を調整
pgbench -h ホスト名 -U ユーザー名 -p ポート番号 -d データベース名 -T 300(テストしたい期間秒数) -c 10(想定クライアント数) -j 2(スレッド数)

# select文のみ
pgbench -h ホスト名 -U ユーザー名 -p ポート番号 -d データベース名 -T 300(テストしたい期間秒数) -c 10(想定クライアント数) -S
```

結果の見方

```sh
transaction type: <builtin: TPC-B (sort of)>            #   使用されたトランザクションのタイプ
scaling factor: 1                                       #   スケーリングファクター(テストデータの量を調整するための指標)
query mode: simple                                      #   クエリのモード
number of clients: 10                                   #   テスト実行時のクライアント数
number of threads: 1                                    #   使用されたスレッド数
duration: 300 s                                         #   テストが実行された期間（秒単位）
number of transactions actually processed: 99999        #   実際に処理されたトランザクション数
latency average = 11.111 ms                             #   平均レイテンシ（遅延時間/ミリ秒単位）
tps = 111.111111 (including connections establishing)   #   秒間トランザクション数（TPS/接続確立を含む場合の値)
tps = 111.222222 (excluding connections establishing)   #   秒間トランザクション数（TPS/接続確立を除いた場合の値）
```

[mysqlとpsql](https://qiita.com/aosho235/items/c657e2fcd15fa0647471)
