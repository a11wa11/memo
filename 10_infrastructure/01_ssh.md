# ssh

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

### 初の接続先の時の「Are you sure you want to continue connecting(yes/no)?」を聞かれないようにする

```sh
ssh -oStrictHostKeyChecking=no 接続先名
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

## ポートフォワード

```sh
ssh -L ローカルポート:リモートホスト:リモートポート ユーザ名@SSH接続ホスト名
```

## その他

```sh
# 接続テスト。実際にログインはしない
ssh -T github.com
# 成功の場合
## Hi ユーザー名! You've successfully authenticated, but GitHub does not provide shell access.

# 失敗の場合
## Permission denied (publickey).
```

## [ssh-keygen](https://www.attachmate.com/ja-jp/documentation/reflection-desktop-v16/rdesktop-guide/data/ssh-keygen_command_rf.htm)

### 鍵の作成

```sh
ssh-keygen -t rsa -b 4096 -C "email@example.com" -f ~/.ssh/id_rsa(名付けたい鍵名)
```

### 接続先の情報を削除

```sh
# .ssh/known_hostsを直接編集してもOK
ssh-keygen -R remote_host_name
```

[参考ページ](https://note.crohaco.net/2017/ssh-tunnel/)

### 公開鍵のコピーを作成

```sh
ssh-keygen -y -f 秘密鍵の指定
```
