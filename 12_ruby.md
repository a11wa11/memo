# ruby

### 記法
* 真偽値を返すメソッドは、メソッド名の末尾に?を付ける慣習
<details>
  <summary>コード</summary>

```
def natural_number?(number)
  return number > 0
end

puts natural_number?(3) 

```
</details>

## bundle

* bundle install --path <フォルダ名>とすれば、指定のフォルダにインストール先を指定できる。指定先は`vendor/bundle`が一般的
* 一度上のコマンドを実行すれば、Bundlerはインストール先を記憶するので次回以降はbundle installを実行するだけで毎回vendor/bundleディレクトリにインストールされる
* railsアプリのルートディレクトリの下のvendor/bundleディレクトリにインストールされ、そのアプリケーションの中だけにインストールされる
* Bundlerはgemのインストール先を、railsアプリのルートディレクトリの下の、.bundle/configというファイルで記憶している

<details>
  <summary>コード</summary>

```
# gemのインストール
bundle install --path vendor/bundle
bundle update
```
</details>

### エラー解消
* エラー検索方法
  * `is:issue is:open キーワード` githubで検索する
  * スタックオーバーフローは✔がベストアンサー、しかし投票数が多いものの方が参考すべき

<details>
  <summary>コード</summary>

```
gem "better errors"
gem "binding of caller"

byebug # railsに恐らくデフォルトで入っている。import pdb;pdb.trace()のように仕込めばデバッグ可能
```
</details>


## Ruby環境構築(AWS-amazonlinux)  -> [参考](https://qiita.com/Ekodhikodhi/items/01eab1b2b5785163e684)

<details>
  <summary>1. rbenvのインストール</summary>

```
# yum更新
sudo yum update -y

# gitのインストール
sudo yum install git -y

# rbenvをリポジトリからクローン
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv

# クローンしてきたらrbenvのPATHを通す
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
source ~/.bash_profile

# rbenvのバージョンが表示されればインストールは成功
rbenv -v
```
</details>

<details>
  <summary>2. ruby-buildのインストール  

`※Rubyをrbenv経由でインストールする時に必要なrbenvのプラグイン。これ入ってないとRubyインストール不可`
</summary>

```
# リポジトリからクローン
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

# インストールの実行
cd ~/.rbenv/plugins/ruby-build
sudo ./install.sh

# インストール可能なRubyのバージョン一覧が表示されればruby-buildのインストールは成功
rbenv install -l
```
</details>

<details>
  <summary>3. Rubyのインストール</summary>

```
# Rubyインストールに必要なパッケージをインストール
sudo yum -y install gcc-c++ glibc-headers openssl-devel readline libyaml-devel readline-devel zlib zlib-devel libffi-devel libxml2 libxslt libxml2-devel libxslt-devel sqlite-devel

# rbenvでバージョンを指定してRubyをインストール
rbenv install バージョン名

# rbenvで使用するRubyのバージョンを指定
rbenv global 2.7.0

# Rubyのバージョンを確認する（指定したバージョンが表示されれば成功）
ruby -v
```
</details>

## Rails
* 外部からのアクセスを可能にする

```
rails s -b 0.0.0.0
```

* rails new

```
# バージョンを指定して rails new
rails _5.1.6_ new アプリ名
cd アプリ名

# javascriptがない時にエラーが起こる場合、nodejsをインストール
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
```

* Railsでmysql使用
  1. mysqlに接続するアダプターのgemを追加する

```
# Gemfile
gem 'mysql2'
bundle install --path vendor/bundle
# mysql-develないとエラーになるため、インストール
sudo yum install mysql-devel
```
  2. 最初からmysqlを使用する際はオプションあり

```
rails _5.1.6_ new english_words -d mysql
# 後、config/database.ymlの接続設定を行う
```

* 簡易アプリ制作例。Wordクラスのテーブルを作成し、english,japanese,remarksの3カラムを作成

```
rails generate scaffold Word english:string japanese:string remarks:string
rails db:create
# DBを作成する。下と同じ
>mysql
create database words;

rails db:migrate
rails db:seed
```

* 初期データ投入
  1. `db/seeds.rb`を編集。以下参考文

```
require "csv"

CSV.foreach('db/words.csv') do |info|
  Word.create(:english => info[0], :japanese => info[1], :remarks => info[2], :created_at => info[3], :updated_at => info[4])
end
```
  2. 初期データを反映させる

```
rails db:seed
```

### railsコマンド
* モデルの作成

```
rails generate model モデル名 フィールド名1:データ型1 ...
```
* コントローラーの作成

```
rails generate controller StaticPages home help
```
* generateの取り消し

```
rails destroy controller StaticPages home help
rails destroy model User
```
* マイグレーションの取り消し

```
# 1つ前の状態に戻る
rails db:rollback

# 最初の状態に戻る
rails db:migrate VERSION=0
```
* 正規コマンドと省略コマンド

| 完全なコマンド | 短縮形コマンド |
| - | - |
| rails server | rails s |
| rails console	| rails c |
| rails generate | rails g |
| rails test | rails t |
| bundle install | bundle |


* test

```
rails test
```

* test通るための一連の手順
test/controllers/static_pages_controller_test.rbで失敗させたいテストを書く

config/route.rbでルーティング作成
controllerでアクション追加
viewでhtmlファイル作成
↑でtest通る


```
test/test_helper.rb

require "minitest/reporters"
Minitest::Reporters.use!
```


```
"#{foo} bar" # ダブルクオテーションでは式展開される
'#{foo} bar' # シングルクオテーションでは式展開されない
```
* よく使うメソッド

```
>> "foobar".length        # 文字列に "length" というメッセージを送る
=> 6

>> "".empty?
=> true

>> nil.to_s
=> ""

>> a = %w[foo bar baz quux]         # %wを使って文字列の配列に変換
=> ["foo", "bar", "baz", "quux"]


>> (1..5).each { |i| puts 2 * i }
2
4
6
8
10
=> 1..5

>> (1..5).each do |i|
?>   puts 2 * i
>> end
2
4
6
8
10
=> 1..5

# シンボル
user = { :name => "Michael Hartl", :email => "michael@example.com" }
{ name: "Michael Hartl", email: "michael@example.com" }


# なお、論理値はそれぞれ && (and) や || (or)、! (not) オペレーターで表すこともできます。


```

### Railtutorial  5章

* ヘルパー

```
link_to
```
* navタグには「その内側がナビゲーションリンクである」という意図を明示的に伝える役割があり
* yieldメソッドはWebサイトのレイアウトにページごとの内容を挿入します

<details>
  <summary>コード</summary>
</details>


* パーシャル
  - 用途はコード整理
  - 別ファイルに保存したコードを呼び出す
  - 呼び出し元での呼出方　`<%= render 'ディレクトリ/パーシャル名' %>`
  - 呼び出し先でのファイル名　`'ディレクトリ_/パーシャル名'`  ※アンダースコアを書く
* Railsのルーティング
* Asset Pipeline
CSS、JavaScript、画像などの静的コンテンツの生産性と管理を大幅に強化するのが「アセットパイプライン (Asset Pipeline)」
Asset Pipelineの最大のメリットの1つは、本番のアプリケーションで効率的になるように最適化されたアセットも自動的に生成されることです。

* Sass
CSS生成ツール
cssのネストが可能
```
#logo {
  float: left;
  margin-right: 10px;
  font-size: 1.7em;
  color: #fff;
  text-transform: uppercase;
  letter-spacing: -1px;
  padding-top: 9px;
  font-weight: bold;
  &:hover {
    color: #fff;
    text-decoration: none;
  }
}
```
変数を使用することが可能
`light-gray: #777;`

```
unless 条件式 then
  条件式が偽の時に実行する処理
end
```


* 参考
#### [マイグレーション](https://railsguides.jp/active_record_migrations.html)
#### [git 万葉](https://github.com/everyleaf/el-training)
#### [rails 設定](https://railsguides.jp/configuring.html)
https://jpcloud.net/q/qheojrok