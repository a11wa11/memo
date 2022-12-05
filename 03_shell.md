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
# INPUTのみ採番して制限内容を表示する
iptables -nL INPUT --line-numbers

# 制限するときの例
iptables -A INPUT -p tcp -s 10.0.0.0(IPアドレス) --sport 3000(ポート番号) --tcp-flags SYN,FIN,RST,ACK FIN,ACK -j DROP
iptables -A INPUT -p tcp -s 10.0.0.0(IPアドレス) --sport 3000(ポート番号) --tcp-flags PSH,ACK PSH,ACK -j DROP


# 下記のファイルのIPADDRの変更などでIPアドレスを変更可能
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

## パフォーマンス測定

* top(プロセスの状態表示)
  * load-averageはディスクIO待ちのプロセス数を表す。左から1分、5分、15分平均で表示される
  * CPU表示 us->ユーザー空間プロセスの時間割合 sy->カーネル空間プロセスの時間割合 id->idle状態の時間割合
  * メモリ表示
  * total = used + buff/cache + free
  * buff/cache = 解放不可の領域と解放可能の領域があり、よくfreeの領域が減ることと連動してbuff/cacheが増える
  * Avail Mem = free + buff/cache(解放可能領域)

```sh
# リアルタイムにプロセスの状態表示が可能
top

q 終了
? or help ヘルプ表示
P CPU率でソート
M メモリ使用率でソート
```

* vmstat 秒サイクル 表示回数
  * vmstatコマンド例 vmstat 秒サイクル 表示回数
  * 最初の１例目は当てにならない(サーバ起動時からの統計値となり、リアルタイム性がない)

```sh
vmstat 2 7
```

* sar (System Activity Report)
  * CPU、Memoryメモリ、ネットワーク、I/Oなどの付加状況を表示する
  * 過去に遡って確認可能
  * 低負荷のため本番環境でもパフォーマンス影響は少ない

```sh
# CPU使用率
sar -P ALL
# メモリ使用率
sar -r
# ネットワーク負荷
sar -n DEV
# ディスクI/O
sar -d -p
# ファイル保管場所指定
sar -r -f /var/log/ファイル名
```

* netstat
  * ネットワーク接続やルーティングの状況、ネットワークインターフェースの状態を表示する
  * CentOS7, RHEL7では非推奨(代替 ss)。ただしssの不具合も報告されている

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

# list block devices
lsblk
```

---

### 名前解決

```sh
nslookup
>google.com
```

### ファイルタイプ確認

```sh
file -s 対象ファイル
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

### 稼働時間確認

```sh
uptime
>0:24  up 12 days,  1:36, 3 users, load averages: 1.69 2.10 2.02
```

---

## 便利コマンド

### md5sum

```sh
md5sum ファイル名
```

### awk

```sh
# 標準出力で表示される内容のうち、空白区切りで２番目の内容を取得
ps aux | grep python | grep -v grep | awk '{print $2}'
# 標準出力で表示される内容のうち、空白区切りで4番目の内容を取得
ps aux | grep python | grep -v grep | awk '{print $4}'
# 標準出力で表示される内容のうち、空白区切りで最後の内容を取得
ps aux | grep python | grep -v grep | awk '{print $NF}'
```

### 時間変更

```sh
date -s "2099/12/31 23:00"  # 指定時間まで時間を進める

hwclock -s                  # 変更した時間を元に戻す
```

### 時間指定

```sh
yum install at
systemctl start atd

at now + 5min
> 処理内容を記述し、Ctl + D で終了

atq    # atコマンドで登録している一覧を表示する
atrm 番号　#　登録しているat内容を削除する

/etc/at.allow # 許可ユーザーを登録
/etc/at.deny  # 拒否ユーザーを登録
```

### lessの設定

```sh
# 結果の折り返しを無効にする
export LESS="-XFR"

# 結果の折り返しを有効にする
export LESS="-SRXF"
```

### 画像ダウンロード(curl)

```sh
# curl -o 保管ディククトリ/保管ファイル名 -OL ダウンロードしたいURL
curl -o app/assets/images/rails.png -OL railstutorial.jp/rails.png
```

### cron

```cron
# 分(0-59) 時(0-23) 日(1-31) 月(1-12) 曜日(0-7) # 0と7は日曜日
# 曜日 0,7=日 1=月 2=火 3=水 4=木 5=金 6=土

0 * * * * sh /home/user/test.sh       # 毎時0分に実行
*/4 * * * * echo "test" >> ~/test.txt # 4分毎に実行

# crondを確認
systemctl status crond.service

# crondを起動
systemctl start crond.service

# 登録されているcronを表示
crontab -l

# cronを編集
crontab -e

# 実行結果を確認
cat /var/log/cron

# 設定ファイルを表示
cat /etc/crontab

SHELL  # cronで使用するshellのこと
PATH   # cronに教えるpath
MAILTO # cron実行結果を送るユーザー、またはメールアドレスを指定
HOME   # cronを実行するカレントディレクトリを設定
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

* 文字列から配列作成

```sh
${変数名/置換前文字列/置換後文字列} #文字列置換

PYTHON_VERSION="3.9.14"
PYTHON_VERSION_NUNBERS=${PYTHON_VERSION/./ } #マッチした先頭を置換したい場合
echo ${PYTHON_VERSION_NUNBERS[0]} # 3
echo ${PYTHON_VERSION_NUNBERS[1]} # 9.14

PYTHON_VERSION_NUNBERS=${PYTHON_VERSION//./ } #マッチする文字をすべて置換したい場合
echo ${PYTHON_VERSION_NUNBERS[0]} # 3
echo ${PYTHON_VERSION_NUNBERS[1]} # 9
echo ${PYTHON_VERSION_NUNBERS[2]} # 14
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
if [[ argA = "-f" ]] || [[ argA = "--force" ]]  # argAが -f または --force の場合
if [[ "$str" == *shell* ]]   # $str がshellという文字列を含むかどうか
```

* case文

```sh
read -p "Are you sure you're trying to delete the stack? [y/n]: " YN
case $YN in
    [Yy]|[Yy][Ee][Ss])
        echo "Start delete the Stack"
        ;;
    [Nn]|[Nn][Oo])
        echo "Canceled delete Stack"
        exit 1
        ;;
    *)
        echo "No answer. Please enter your answer"
        exit 1
        ;;
esac
```

* for文

```sh
# 基本
for var in `seq 1 10`;do  echo $var;done

for var in `seq 1 2 10`; # 1 3 5 7 9
for var in ls pwd date;  # コマンド実行も可能

# 一括でファイルをリネームしたい場合 → https://orebibou.com/ja/home/201601/20160105_001/
for i in `ls`; do cp $i `echo $i | sed "s/2021-01-01T/2022-12-31T/g"`;done
```

* ファイル作成

```sh
touch -d '2019/01/01' sample.txt # 作成日付を指定
```

* 置換

```sh
# 基本文
sed -e "s/(置換前の文字)/(置換後の文字)/g"
sed -e "s/(置換前の文字)/(置換後の文字)/g" ファイル名
sed -i -e "s/(置換前の文字)/(置換後の文字)/g" ファイル名 # 直接ファイルを書き換える

sed -e "3 s/(置換前の文字)/(置換後の文字)/g" ファイル名  # ３行目の対象を置換する
sed -i -e "3i hoge" ファイル名                       # ３行目にhogeを挿入する
sed -e "s/$str/hoge/g"                             # 変数を使用する時 → ""で囲み、$変数名と記載
```

* && 前段の処理が成功したら後段の処理をする

```sh
yum update -y && yum install tree
```

* 関数

```sh

function 関数名 (引数) {
  echo "message: ${MSG:-HELLO}" # ${変数名:-代入内容}で変数の初期値を定義可能
}
```

* echo

```sh
# 改行させる
echo;
echo "test"
```

* 入力待ち

```sh
# -pで指定した文を表示し、入力した内容をYN変数で定義
read -p "Are you sure you're trying to delete the stack? [y/n]: " YN
```

* jq

```sh
# 特定の値を取得する
cat ***.json | jq ".キー名"
# row形式で表示(ダブルクオテーションの除去など)
cat ***.json | jq -r
# resultという任意のキーを作成してその内容を決定
cat ***.json | jq -r '.StackSummaries[] | .任意のキー = .StackStatus + " " + .LastUpdatedTime + " " + .StackName | .任意のキー'
```

* ヒアドキュメント 標準入力から受け取れる

```sh
tee /***/ファイル名<<EOF
echo "Yeah"
EOF
# tee /***/ファイル名<<"EOF" とEOFを""か''で囲むと中身を変数展開しない
```

* ジョブ １つ以上のコマンドで動作するひとまとまりの処理単位

```sh
#実行中のジョブを表示
jobs

fg %ジョブ番号
bg %ジョブ番号
```

* 日付取得

```sh
# yyyymmddの形で取得
date '+%Y%m%d'
# yyyymmdd-HHMMの形で取得
date '+%Y%m%d-%H%M'
```

* デバッグ

```sh
sh -x 対象スクリプト
bash -vx 対象スクリプト
```
