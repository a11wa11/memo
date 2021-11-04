# shell

## zsh プロンプト設定

[参考](http://tegetegekibaru.blogspot.com/2012/08/zsh_2.html)

<details>
<sammary>タイトル</sammary>
<pre><code>
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
</code></pre>
</details>

---

## システム情報

### ホストネーム

* ホスト名を確認

```sh
cat /etc/hostname  
hostnamectl status 
```

* ホスト名変更

```sh
hostnamectl set-hostname ホストネーム名
```

### ユーザー確認

```sh
/etc/passwd
/etc/shadow
```

### タイムゾーン

* タイムゾーンを確認

```sh
timedatectl status
cat /etc/localtime
```

* タイムゾーン変更

```sh
cp /etc/localtime /etc/localtime.org # バックアップ
ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
```

### ロケール

```sh
# ロケールを確認
locale
localectl

# ロケール変更
export LC_ALL=ja_JP.UTF-8
```

### IPアドレス関連

```sh
# IPアドレスの確認
ip a
ip addr show
ifconfig # 古くて今はあまり推奨されていない

# 特定のアクセスを制限したり、逆に許可したりし、その制限を確認する
iptables -L

# 下記のファイルのPADDRの変更などでIPアドレスを変更可能
/etc/sysconfig/network-scripts/ifcfg*
```

* ネットワーク設定を反映

```sh
systemctl restart network
```

### ポート確認

```sh
netstat -an | grep ポートナンバー
ss | grep ポートナンバー
```

### ネットワークマネージャーコマンド

```sh
nmcli d
nmcli device show ens160
```

### サービス一覧確認

```sh
# 定義されているサービス一覧と自動起動の状態
systemctl list-unit-files --type=service
```

---

## ハードウェア情報

* メモリ

```sh
free 
free -m       # メガバイト
free -g       # ギガバイト
free -h       # 適切な形態に併せて表示
free --total  # 空き領域の合計も併せて表示する

```

* ディスク

```sh
df
df -kP      # ブロックサイズが1kで表示 P=POSIX出力形式
df -i       # i=inode情報を表示する
df -h       # サイズに応じて読みやすい単位で表示する
df --total  # 空き領域の合計も併せて表示する

# 各ディレクトリやファイルごとに確認
du -sh ./*
du -h -d 1 . # カレントディレクトリ下の角ファイルやディレクトリのサイズを確認
```

---

### 名前解決

```sh
nslookup
>google.com
```

### ファイル上限数確認

```sh
ulimit -n ディレクトリ名
```

### nice値関連

```sh
# nice値確認
ps -l
ps -c stress -o pid,ppid,args,ni,psr,wchan
top 

# nice値を変更して実行
nice -n 5 python sample.py
nice -20 [command]
```

---

## 便利コマンド

### md5sum

```sh
md5sum ファイル名
```

### lessの設定

```sh
# 結果の折り返しを無効にする
export LESS="-XFR"

# 結界の折り返しを有効にする
export LESS="-SRXF"
```

### 画像ダウンロード(curl)

```sh
# curl -o 保管ディククトリ/保管ファイル名 -OL ダウンロードしたいURL
curl -o app/assets/images/rails.png -OL railstutorial.jp/rails.png
```

### cron

```cron
*/4 * * * * sh /home/user/test.sh
*/4 * * * * exho "test" >> ~/test.txt
```

### 基本コマンド

* 四則演算

```sh
$(( 99 * 99 ))
$(( var1 * var2 ))
$(expr 99 ¥* 99 )
$(expr 99 / 99 )
$(expr $var1 + $var2 )

# 小数点を含めた計算はbcを使用
yum install bc
echo "43.8 * 23.9" | bc 
```

* if文

```sh
# ファイルの存在確認
if [ -e ファイル名:]      # ディレクトリ or ファイルが存在すること 
if [ -f ファイル名:]      # ディレクトリではなくファイルが存在すること 
if [ -d ファイル名:]      # ディレクトリが存在するか
if [ -s ファイル名:]      # ディレクトリ or 中身のあるファイルかどうか
if [ -w ファイル名:]      # 書込権限があるかどうか
if [ -x ファイル名:]      # 実行権限があるかどうか
if [ fileA -nt fileB:]  # fileAがfileBよr新しいかどうか
if [ fileA -ot fileB:]  # fileAがfileBよr古いかどうか

```

* for文

```sh
# 基本
for var in `seq 1 10`;do  echo $var;done

for var in `seq 1 2 10`; # 1 3 5 7 9
for var in ls pwd date;  # コマンド実行も可能
```

* ファイル作成

```sh
touch -d '2019/01/01' sample.txt # 作成日付を指定
```
