# Linux Command Line

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