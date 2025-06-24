# slack

## コマンド

- webhookURLでメッセージを発信する
  - application/json POSTリクエストのボディとしてJSONでメッセージを送信

```sh
curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' https://hooks.slack.com/services/XXXXXXXXXXX/YYYYYYYYYYY/ZZZZZZZZZZZZZZZZZZZZZZZZ
```

- [webhook参考](https://zenn.dev/hotaka_noda/articles/4a6f0ccee73a18)
