# shell

- [コマンド](#コマンド)
- [便利コマンド](#便利コマンド)
- [シグナル](#シグナル)
- [プロンプト](#プロンプト)

## コマンド

- [基本](#基本)
  - [四則演算](#四則演算)
  - [リンク関連](#リンク関連)
  - [if](#if)
  - [case](#case)
  - [ループ](#ループ)
  - [関数](#関数)
  - [文字列](#文字列)
  - [検索系](#検索系)
  - [抽出系](#抽出系)
  - [入力待ち](#入力待ち)
  - [ファイル系](#ファイル系)
- [ユーザー系](#ユーザー系)
  - [ユーザー確認](#ユーザー確認)
  - [グループ確認](#グループ確認)
  - [権限変更](#権限変更)
- [デバッグ](#デバッグ)
- [記号](#記号)

### 基本

#### 四則演算

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

#### リンク関連

```sh
# シンボリックリンク貼り付け
ln -sf リンク先パス リンク名
# python3を標準にする
alternatives --set python /usr/bin/python3
```

#### if

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
if [ -z str:]           # 文字列 str が0文字であれば(zero)
```

#### case

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

#### ループ

- for

```sh
# 基本
for var in `seq 1 10`;do  echo $var;done

for var in `seq 1 2 10`; # 1 3 5 7 9
for var in ls pwd date;  # コマンド実行も可能

# 一括でファイルをリネームしたい場合 → https://orebibou.com/ja/home/201601/20160105_001/
for i in `ls`; do cp $i `echo $i | sed "s/2021-01-01T/2022-12-31T/g"`;done
```

- while

```sh
var=1
while (( $var < 10)); # 10以下である限り
do
  echo $var
done
```

- until

```sh
var=1
until (( $var > 10)); # 10以上になるまでは
do
  echo $var
done
```

#### 関数

```sh

function 関数名 (引数) {
  echo "message: ${MSG:-HELLO}" # ${変数名:-代入内容}で変数の初期値を定義可能
}
```

#### 文字列

##### echo

```sh
# 改行させる
echo;
echo "test"
```

##### 文字列から配列作成

```sh
${変数名/置換前文字列/置換後文字列} #文字列置換

PYTHON_VERSION="3.9.14"
PYTHON_VERSION_NUMBERS=${PYTHON_VERSION/./ } #マッチした先頭を置換したい場合
echo ${PYTHON_VERSION_NUMBERS[0]} # 3
echo ${PYTHON_VERSION_NUMBERS[1]} # 9.14

PYTHON_VERSION_NUMBERS=${PYTHON_VERSION//./ } #マッチする文字をすべて置換したい場合
echo ${PYTHON_VERSION_NUMBERS[0]} # 3
echo ${PYTHON_VERSION_NUMBERS[1]} # 9
echo ${PYTHON_VERSION_NUMBERS[2]} # 14
```

##### 置換

```sh
# 基本文
sed -e "s/(置換前の文字)/(置換後の文字)/g"
sed -e "s/(置換前の文字)/(置換後の文字)/g" -e "s/(置換前の文字)/(置換後の文字)/g" # 2回置換する
sed -e "s/(置換前の文字)/(置換後の文字)/g" ファイル名
sed -i -e "s/(置換前の文字)/(置換後の文字)/g" ファイル名 # 直接ファイルを書き換える

sed -e "3 s/(置換前の文字)/(置換後の文字)/g" ファイル名  # ３行目の対象を置換する
sed -i -e "3i hoge" ファイル名                       # ３行目にhogeを挿入する
sed -e "s/$str/hoge/g"                             # 変数を使用する時 → ""で囲み、$変数名と記載
```

#### 検索系

- find

```sh
find 対象ディレクトリ -name 検索対象名
find 対象ディレクトリ -name 検索対象名*  # 正規表現検索可能
find 対象ディレクトリ -type f          # ファイルを検索
find 対象ディレクトリ -type d          # ディレクトリを検索
find 対象ディレクトリ -type s          # シンボリックリンクを検索
find 対象ディレクトリ -user ユーザー名   # 所有ユーザーで検索
find 対象ディレクトリ -atime -3        # 3日前から後の日にちのアクセス日のものを検索
find 対象ディレクトリ -atime +3        # 3日前から以前の日にちのアクセス日のものを検索
find 対象ディレクトリ -mtime -3        # 3日前から後の日にちの更新日のものを検索
find 対象ディレクトリ -mtime +3        # 3日前から以前の日にちの更新日のものを検索
find 対象ディレクトリ -amin -10        # 10分前から後の日にちのアクセス日のものを検索
find 対象ディレクトリ -name 検索対象名 -exec cat {} \; # 検索したものに対して-execで指定したコマンドを実行する
```

- locate

```sh
# 1. インストール
yum install -y mlocate
# 2. DB登録
updatedb # /etc/updatedb.conf に設定が保存されている
# 実行
locate "*.txt" # .txtの形のファイルを検索する
```

- type

```sh
type cat # コマンドが実行ファイルなのか組込関数かエイリアスか確認する
```

- whereis

```sh
whereis find
# コマンド場所 設定ファイル マニュアルなどの場所を表示する　
> find: /usr/bin/find /usr/share/man/man1/find.1.gz /usr/share/info/find.info.gz
```

#### 抽出系

##### grep

```sh
# grepで複数ワード検索
grep -e 文字列１ -e 文字列2 ディレクトリ

# 前後に指定した行数を表示
grep -C 5(指定行数) 文字列 ディレクトリ

# 再起的
grep -r 文字列 ディレクトリ

# 大文字小文字区別なし
grep -i 文字列 ディレクトリ
```

##### awk

```sh
# 標準出力で表示される内容のうち、空白区切りで２番目の内容を取得
ps aux | grep python | grep -v grep | awk '{print $2}'
# 標準出力で表示される内容のうち、空白区切りで4番目の内容を取得
ps aux | grep python | grep -v grep | awk '{print $4}'
# 標準出力で表示される内容のうち、空白区切りで最後の内容を取得
ps aux | grep python | grep -v grep | awk '{print $NF}'
```

##### xargs

```sh
# 標準入力から読み取った値(最初のARGS)に後のARGSに置き換える
echo "test.txt" | xargs -I ARGS echo ARGS
```

#### 入力待ち

```sh
# -pで指定した文を表示し、入力した内容をYN変数で定義
read -p "Are you sure you're trying to delete the stack? [y/n]: " YN
```

#### ファイル系

- 作成日付を指定

```sh
touch -d '2019/01/01' sample.txt
```

- ファイル確認

```sh
# どのタイプのファイルか確認
file 対象のパス

# ファイルシステムステータスを表示
stat 対象のパス
```

### ユーザー系

#### ユーザー確認

```sh
/etc/passwd
/etc/shadow

$ id
uid=0(root) gid=0(root) groups=0(root)

```

#### グループ確認

```sh
groups
cat /etc/gshadow
getent group グループ名 # 存在するか確認
```

#### 権限変更

```sh
# ユーザー追加
useradd -m -s /bin/bash -U 新規ユーザー名
# -m：ホームディレクトリを作成する
# -s：デフォルトのシェルを指定する
# -U：新しいグループを作成する
useradd -r グループ名
# -r 一般ユーザーアカウントとは異なる範囲のUIDを持つシステムアカウントを作成
useradd -u 101 -g 101 -r グループ名
# -u ユーザーID番号を指定する
# -g グループID番号を指定する

# グループ追加
groupadd グループ名
groupadd -g 101(グループID) グループ名 # グループIDを指定

# 消去
userdel ユーザー名
groupdel グループ名

# ユーザーをグループに追加 -aG = 既存グループを維持しながらユーザーを指定をグループに追加
usermod -aG グループ名 ユーザー名

# 再起的にオーナーを変更する
chown -R ユーザー名:グループ名 対象フォルダ
```

### デバッグ

```sh
# スクリプト実行時
sh -x 対象スクリプト
bash -vx 対象スクリプト

# 指定箇所のみデバッグ
set -x      # ここからデバッグ
echo 'test' # 指定箇所
set +x      # ここでデバッグ終了
```

- strings

バイナリファイルからテキスト文字列を抽出するコマンド

```sh
strings 対象パス
```

### 記号

#### &&

- 前段の処理が成功したら後段の処理をする

```sh
yum update -y && yum install tree
```

#### 特殊変数
  
- `$1` 1番目の引数
- `$3` 3番目の引数
- `$*` すべての引数。引数分の数が3つでも1つ分として受け取る
- `$@` すべての引数。引数分の数が3つなら3つ分として受け取る
- `$$` プロセスID

- `--`
  - `コマンド -- 引数`のように打つと`--`以降は引数と解釈される
  - 例 `npm run test -- 引数1 引数2`

## シグナル

```sh
kill -l               # シグナルリスト確認
man bash              # signalで検索するとsignalに関する章があるので確認可能

# trapコマンド
trap 実行したいコマンド内容 シグナル番号orシグナル名 

trap "echo プロセス終了しました" 0             # プロセス終了時に標準出力
trap "echo Ctl+Cで割り込み終了しました" SIGINT # プロセス終了時に標準出力
```

- nohup <"no hang up"（ハングアップしない>

```sh
# 指定したコマンドがバックグラウンドで実行され、制御端末の切断やログアウトに関係なく実行が継続される。長時間かかるコマンドに便利
nohup 実行したいコマンド
```

## 便利コマンド

- [時刻系](#時刻系)
- [less](#less)
- [curl](#curl)
- [jq](#jq)
- [cron](#cron)
- [ヒアドキュメント](#ヒアドキュメント)
- [ステータス](#ステータス)
- [ブレース展開](#ブレース展開)

### 時刻系

#### 日付取得

```sh
# yyyymmddの形で取得
date '+%Y%m%d'
# yyyymmdd-HHMMの形で取得
date '+%Y%m%d-%H%M'
```

#### 時間変更

```sh
date -s "2099/12/31 23:00"  # 指定時間まで時間を進める

hwclock -s                  # 変更した時間を元に戻す
```

#### 時間指定

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

### less

- 設定

```sh
# 結果の折り返しを無効にする
export LESS="-XFR"

# 結果の折り返しを有効にする
export LESS="-SRXF"
```

### curl

- `-o` 保管ディククトリ/保管ファイル名の指定
- `-O` ダウンロードファイルを変更せずにそのままの名前で保存
- `-L,--location` サーバーがリダイレクトを返した際に追跡してファイルをダウンロード
- `-k` サーバー証明書の検証を無効にする
- `-u,--user` 認証情報を指定する `-u username:password`と指定すると指定されたユーザー名とパスワードで認証される

```sh
# ダウンロード
curl -OL ファイルURL  # wget ファイルURL と同じ
# 命名してダウンロード `curl -o 保管ディククトリ/保管ファイル名 -L ダウンロードしたいURL`
curl -o app/assets/images/rails.png -OL railstutorial.jp/rails.png
# ヘッダー表示
curl -Iv 対象URI
# User-Agent設定
curl --user-agent 任意のUser-Agent名 -Iv 対象URI
```

### jq

```sh
# 特定の値を取得する
cat ***.json | jq ".キー名"
# row形式で表示(ダブルクオテーションの除去など)
cat ***.json | jq -r
# resultという任意のキーを作成してその内容を決定
cat ***.json | jq -r '.StackSummaries[] | .任意のキー = .StackStatus + " " + .LastUpdatedTime + " " + .StackName | .任意のキー'
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

### ヒアドキュメント

- 標準入力から受け取れる

```sh
tee /***/ファイル名<<EOF
echo "Yeah"
EOF
# tee /***/ファイル名<<"EOF" とEOFを""か''で囲むと中身を変数展開しない
```

### 変数

- envsubst
  - テンプレートファイル内に記述された環境変数を実際の値に置き換える
  - テンプレートファイルで`${VARIABLE_NAME}`の形式で記述された変数を対応する環境変数の値に置換する

```sh
# 基本形式(テンプレート内の${VARIABLE_NAME}をすべて置換)
envsubst  < template.txt
# 指定したテンプレート内の${VARIABLE_NAME}のみ置換
envsubst '${USER}' < template.txt
```

### ステータス

- ジョブ １つ以上のコマンドで動作するひとまとまりの処理単位

```sh
#実行中のジョブを表示
jobs

fg %ジョブ番号
bg %ジョブ番号
```

- プロセス(ps コマンドの見方)

| 項目 | 説明 |
| - | - |
| USER | プロセスの所有ユーザー |
| PID | プロセス番号 |
| %CPU | CPUの占有率 |
| MEM | 実メモリでの占有率 |
| VSZ | 仮想メモリまで含んだメモリ上の使用サイズ(Kバイト) |
| RSS | 実メモリ上の使用サイズ(Kバイト) |
| TTY | 端末名 |
| STAT | プロセスの状態 / S+ -> データの読み込みや書き込みのイベントが終わるのを待っているなど止まっているわけではない. Sは一時停止。+はフォアグラウンド状態 |
| START | プロセスの開始時刻 |
| TIME | プロセスの総実行時間 |
| COMMAND | 実行コマンド名とパス |

- プロセスツリー

```sh
# pid表示
pstree -p
# 指定のPIDを親として表示
pstree -p PID
# コマンドと引数を表示
pstree -a
```

### ブレース展開

```sh
echo {1..10}
# 1 2 3 4 5 6 7 8 9 10
echo a{m,n}
# am an
```

## プロンプト

### zsh

[参考](http://tegetegekibaru.blogspot.com/2012/08/zsh_2.html)

<details>
<summary>展開</summary>
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

## PATH

- PATHを通す

```sh
echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.zshrc
```

## シェルスクリプト

```sh
#!/bin/bash    この行は、スクリプトがBashシェルで実行されることを指定

# -e エラーが発生したら即座に終了
# -u 未定義の変数を使用したら終了
# もし、任意のコマンドがエラー（終了ステータスが0以外）を返した場合、シェルスクリプトは即座に終了する。これにより、エラーが発生した箇所で処理が途中で終了し、予期せぬエラーが広がることを防げる。

set -eu
```
