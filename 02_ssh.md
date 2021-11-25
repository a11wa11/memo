# ssh

## ssh鍵の作成

```sh
ssh-keygen -t rsa -b 4096 -C "email@example.com" -f ~/.ssh/id_rsa(名付けたい鍵名)
```

## 接続先でタイムアウトになるのを防ぐため、クライアント側で接続時間を伸ばす設定

* `.ssh/config`に以下の設定を書き込む

### 30秒ごとに接続先が生きているか確認

```sh
ServerAliveInterval 30
```

### 200回まで接続先が生きていることを確認

```sh
ServerAliveCountMax 200
```

## 多段接続

```sh
ssh XXX -t ssh YYY
```

## 接続先登録

```sh
Host NAME
    HostName 127.0.0.1
    User USERNAME
    IdentityFile ~/.ssh/SAMPLE.pem
    TCPKeepAlive yes
    IdentitiesOnly yes

    # 鍵のチェックの無効化
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
```

### 接続先の情報を削除

```sh
# .ssh/known_hostsを直接編集してもOK
ssh-keygen -R remote_host_name
```

[参考ページ](https://note.crohaco.net/2017/ssh-tunnel/)