# shell

## zsh プロンプト設定

```
# zshではプロンプト設定は'PROMPT'の変数で設定
# %F{カラー}表示したい文字列%f　→　%Fと%fで囲まれた文字を色変更可能
# %Uと%uで囲まれた文字を下線部表示
# %n = ユーザー名
# %m = ホスト名
# %d = カレントディレクトリ名
# %D = 日付(yy-mm-dd)
# %* = 時間(hh:mm:ss)
PROMT='[%F{yellow}%U%n%u@%m%f]
$ '
# zshでは↑改行したら表示も改行が反映される

# コンソール右側に表示されるプロンプト
RPROMT='[%F{green}%d : %D %*%f]
```

[参考](http://tegetegekibaru.blogspot.com/2012/08/zsh_2.html)


# Network

### ホストネーム
* ホスト名を確認

```
cat /etc/hostname

or

hostnamectl status 

```
* ホスト名変更

```
hostnamectl set-hostname ホストネーム名
```

### ネットワーク
* ネットワーク設定を反映

```
systemctl restart network
```

### タイムゾーン
* タイムゾーンを確認

```
timedatectl status

or

cat /etc/localtime

```

* タイムゾーン変更

```
cp /etc/localtime /etc/localtime.org # バックアップ
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

* ユーザー確認

```
/etc/passwd
/etc/shadow
```

### lessの設定

```
# 結果の折り返しを無効にする
export LESS="-XFR"

# 結界の折り返しを有効にする
export LESS="-SRXF"
```


### 画像ダウンロード

```
# curl -o 保管ディククトリ/保管ファイル名 -OL ダウンロードしたいURL
curl -o app/assets/images/rails.png -OL railstutorial.jp/rails.png
```