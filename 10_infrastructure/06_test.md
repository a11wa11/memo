# testで使えるツール

* メモ アイドリング状態→安定動作時

## Webサーバやアプリケーションサーバの性能テスト

### apache bench

* コマンド
* 
```sh
yum install httpd
ab -n 100 -c 10 -t 5 example.com
# -n リクエスト数
# -c 一度に行うリクエストの数
# -t 何秒間で実施するかのタイムリミット
ab -n 100 -c 10 -A hogehoge:password example.com
# -A 認証に必要なアカウント:パスワードの形式で送信可能
```

* 表記

```sh
Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       63   84  34.0     72     321
Processing:   310 9464 1226.9   9632   13650
Waiting:      261 9463 1227.3   9631   13650
Total:        384 9548 1216.7   9705   13829

# Connect: サーバーに接続するのにかかった時間（ミリ秒）
# Processing: サーバーからのレスポンスを受信するのにかかった時間（ミリ秒）
# Waiting: サーバーからのレスポンスを待つのにかかった時間（ミリ秒）
# Total: リクエスト全体にかかった時間（ミリ秒）
## min  mean[+/-sd] median   max -> 最小値、平均値、標準偏差、中央値、最大値
## +/-sdは、平均値の標準偏差を示す。標準偏差は、測定値のばらつきを示す指標で、値が小さいほど測定値が平均値に近いことを示す
## 例えば、上記の結果のsdは1,226.9ミリ秒であり、測定値が平均値の「9,464ミリ秒 ± 1,226.9ミリ秒」周りに散らばっていることを示す
```

```sh
Server Software:        nginx
Server Hostname:        example.com
Server Port:            443
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES128-GCM-SHA256,2048,128
Server Temp Key:        ECDH P-256 256 bits
TLS Server Name:        example.com

Document Path:          /
Document Length:        366 bytes

Concurrency Level:      100
Time taken for tests:   489.073 seconds
Complete requests:      8000
Failed requests:        0
Non-2xx responses:      8000
Total transferred:      9680000 bytes
HTML transferred:       2928000 bytes
Requests per second:    16.36 [#/sec] (mean)
Time per request:       6113.408 [ms] (mean)
Time per request:       61.134 [ms] (mean, across all concurrent requests)
Transfer rate:          19.33 [Kbytes/sec] received

# Server Software：サーバーで使用されているWebサーバーのソフトウェア名
# Server Hostname：サーバーのホスト名
# Server Port：サーバーがリクエストを受け付けるポート番号
# SSL/TLS Protocol：サーバーとクライアント間で使用されるSSL/TLSプロトコルと、暗号化アルゴリズム
# Server Temp Key：サーバーの一時鍵のアルゴリズムとビット数
# TLS Server Name：SSL/TLSで使用されるサーバーの名前
# Document Path：テストに使用されたドキュメントのパス
# Document Length：テストに使用されたドキュメントの長さ
# Concurrency Level：同時に処理するリクエストの数
# Time taken for tests：テストが完了するまでにかかった時間
# Complete requests：完了したリクエストの数
# Failed requests：失敗したリクエストの数
# Non-2xx responses：2xx以外のレスポンスの数
# Total transferred：サーバーからクライアントに転送された総量（ヘッダーとボディを含む）
# HTML transferred：サーバーからクライアントに転送されたHTMLの量
# Requests per second：秒間のリクエスト数（平均値）
# Time per request：リクエストあたりの平均処理時間（ミリ秒）
# Time per request：全コネクションでのリクエストあたりの平均処理時間（ミリ秒）
# Transfer rate：サーバーからの受信速度（キロバイト/秒）
```


## IPアドレスを確認できるサイト

* [1](https://al-batross.net/2020/11/02/check_my_global_ip_address/)
* [2](http://checkip.dyndns.org/)
* [3](http://httpbin.org/ip)

[SYNフラッド攻撃の対策](https://www.shadan-kun.com/blog/measure/2664/)
