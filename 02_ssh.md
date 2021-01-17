## ssh

### 接続先でタイムアウトになるのを防ぐため、クライアント側で接続時間を伸ばす設定

* `.ssh/config`に以下の設定を書き込む

  * 30秒ごとに接続先が生きている確認
```
ServerAliveInterval 30
```
  * 200回まで接続先が生きていることを確認

```
ServerAliveCountMax 200
```

### 多段接続
```
ssh XXX -t ssh YYY
```


[参考ページ](https://note.crohaco.net/2017/ssh-tunnel/)