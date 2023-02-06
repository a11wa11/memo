# testで使えるツール

* メモ アイドリング状態→安定動作時

## Webサーバやアプリケーションサーバの性能テスト

* apache bench

```sh
yum install httpd
ab -n 100 -c 10 example.com
```

## IPアドレスを確認できるサイト

* https://al-batross.net/2020/11/02/check_my_global_ip_address/
* http://checkip.dyndns.org/
* http://httpbin.org/ip

[SYNフラッド攻撃の対策](https://www.shadan-kun.com/blog/measure/2664/)
