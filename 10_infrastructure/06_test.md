# testで使えるツール

* メモ アイドリング状態→安定動作時

## Webサーバやアプリケーションサーバの性能テスト

### apache bench

* コマンド

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

### [k6](https://k6.io/docs/get-started/running-k6/)

* 実行コマンド

```sh
k6 run script.js(任意のファイルパス)
```

* 実行ファイル(基本)

```script.js
import http from 'k6/http';
import { sleep } from 'k6';
export const options = {
  vus: 10,
  duration: '30s',
};
export default function () {
  http.get('http://test.k6.io');
  sleep(1);
}
```

* 実行ファイル(応用)

```script.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { htmlReport } from "https://raw.githubusercontent.com/benc-uk/k6-reporter/main/dist/bundle.js";
import { textSummary } from "https://jslib.k6.io/k6-summary/0.0.1/index.js";

export const options = {
  duration: '300s',   // 実行時間 基本5分以上が推奨
  vus: 100,           // 同時ユーザー数
// 負荷試験時にオプションを設定したい時に下記コメントアウトを適宜外す
  // iterations: 500,    // 繰り返し実行回数 vusが50ならユーザー数50が各10回ループして系計500回実行する
  // rps: 500,           // 1秒あたりの最大リクエスト数。ただし、公式非推奨のため注意
  // thresholds: {
  //   http_req_failed: ['rate<0.05'],    // リクエストの失敗率は5%未満
  //   http_req_duration: ['p(95)<1000'], // 95%のリクエストで処理時間が1000ms未満
  //   http_req_duration: ["avg<1000"],   // 平均時間が1s未満であること
  // },
  // scenarios: {
  //   constant_request_rate: {             // 任意のシナリオ名
  //     executor: 'constant-arrival-rate', // RPSを制御したい時用のオプション (https://k6.io/docs/using-k6/scenarios/executors/constant-arrival-rate/)
  //     rate: 30,                          // timeUnitで指定された時間毎に反復するテスト回数
  //     timeUnit: '1s',                    // rateを反復させる時間、rateが10なら秒間30リクエスト(10RPS)
  //     preAllocatedVUs: 100,              // 初期に割り当てられるVUS数
  //     maxVUs: 300                        // VUSが足りなかった場合に増える最大VUS数
  //   }
  // },
};

const target = [
  "https://test.k6.io",
  "https://test.k6.io/login",
  "https://test.k6.io/my_messages.php",
];

const endpoints = target;

export default function () {
  for (const endpoint of endpoints) {
    let res = http.get(endpoint)
    check(res, { 'response was success!': (res) => res.status === 200 })
    // 認証が必要な時用のif文
    if (res.status === 401) {
      res = http.post(endpoint, { _password: 'ここに認証パスワードを入力' });
    }
    console.log(`status : ${res.status} --- Response for ${endpoint}`);
    sleep(1);
  }
}

// k6リポーター(https://github.com/benc-uk/k6-reporter)を使用して結果をhtmlファイルへ出力
export function handleSummary(data) {
  // 現在の日付と時刻を取得
  const jstDiffTime = 9 * 60 * 60 * 1000;   // JSTはUTC+9なので、9時間をミリ秒で表現
  const date = new Date();
  const jstDate = new Date(date.getTime() + jstDiffTime);
  const year = jstDate.getFullYear();
  const month = String(jstDate.getMonth() + 1).padStart(2, '0'); // 月を2桁にする
  const day = String(jstDate.getDate()).padStart(2, '0');        // 日を2桁にする
  const hour = String(jstDate.getHours()).padStart(2, '0');      // 時を2桁にする
  const minute = String(jstDate.getMinutes()).padStart(2, '0');  // 分を2桁にする

  // ファイル名を作成
  const filename = `k6/results/result_${year}${month}${day}-${hour}${minute}.html`;

  return {
    // k6リポーターhtmlファイルへ出力
    [filename]: htmlReport(data),
    // k6リポーターhtmlファイル以外にも標準出力で要約を出力
    stdout: textSummary(data, { indent: " ", enableColors: true }),
  };
}
```

参考

* [負荷テストを手軽にできるツール「k6」を試してみた](https://zenn.dev/rescuenow/articles/8349deb470470e)
* [負荷テストツール k6 の基本的な使い方](https://zenn.dev/yumemi_inc/articles/k6-load-test)
* [負荷テストツール K6 について調べてみた](https://sreake.com/blog/learn-about-k6/)
* [イチオシな負荷テストツール k6 の始め方](https://blog1.mammb.com/entry/2023/06/27/090000)
* 試験評価の参考
    * [k6による負荷試験 入門から実践まで](https://speakerdeck.com/fujiwara3/k6niyorufu-he-shi-yan-ru-men-karashi-jian-made)

### [Taurus](https://gettaurus.org/install/Installation/)

使い方

* 以下のymlファイルでテストが可能
    * k6でテストするように設定しているので上記のk6ファイルを流用している

```load_test.yml
execution:
- executor: k6               # k6を使用することを指定
  concurrency: 100           # 同時実行ユーザー数
  hold-for: 5m               # テストを実行し続ける時間
  ramp-up: 1m                # 同時実行ユーザー数が起動するまでの時間(1分かけてユーザー数10にするなど)
  # iterations: 100            # 反復回数
  # throughput: 20             # RPS
  scenario:
    script: ../k6/script.js  # 使用するスクリプトのパスをk6/loadTest.jsに設定
```

* Taurusで負荷試験実行コマンド
    * 以下の条件が揃っている前提
        * 上記の`load_test.yml`という実行ファイル名がカレントディレクトリにある状態
        * `load_test.yml`内にk6で実行する定義があり、k6の実行ファイルはカレントディレクトリの`k6/loadTest.js`になる状態
    * ※`brew install bzt`でもインストールして実行可能だが、依存パッケージが多いのでdockerが無難

```sh
docker run -it --rm -v $(pwd):/bzt-configs blazemeter/taurus load_test.yml
```

* taurusで実行結果をローカルに保存して実行するためのMakefile

```make
now := $(shell date '+%Y%m%d-%H%M')
current_dir := $(shell pwd)
result_dir := $(current_dir)/taurus/results/$(now)

taurus: # taurusをdockerで実行し、結果を`taurus/results/yyyymmdd-hhmm`へ出力する
	mkdir $(result_dir)
	docker run -it --rm -v $(current_dir):/bzt-configs -v $(result_dir):/tmp/artifacts blazemeter/taurus taurus/load_test.yml
	echo "taurus/results/$(now) へ負荷試験の結果が出力されました"

.PHONY: taurus
```

参考

* [手軽に負荷テストができるツール「Taurus」がスゴい](https://zenn.dev/tonchan1216/articles/11afd147ea3dd2734315)
* [負荷テストフレームワークのTaurusはいいぞ](https://engineer.retty.me/entry/2022/12/09/123459)

## stress

* インストール

```sh
yum install stree
stress --version
```

* 使用例

```sh
# CPU負荷がけ
stress -c 1 -q &  # -qはquiet
# IO負荷がけ
stress -d 1 &
# メモリ負荷がけ
stress -m 1
# CPU負荷がけのドライラン(-n)
stress -n -c 1

# stress関連コマンドを消去
pkill stress
```

## ポートスキャン

- nmap
  - ネットワークの調査や監査に使用する
  
```sh
# -sS _ TCP SYNスキャンを実行する
# -sV _ バージョン情報を取得する
# -oN 任意ファイル名 _ 結果をファイルに保存する
nmap -sS -sV -oN nmap-result example.jp(対象ホスト or IP)
```

## IPアドレスを確認できるサイト

* [1](https://al-batross.net/2020/11/02/check_my_global_ip_address/)
* [2](http://checkip.dyndns.org/)
* [3](http://httpbin.org/ip)

[SYNフラッド攻撃の対策](https://www.shadan-kun.com/blog/measure/2664/)
