# ruby

* 真偽値を返すメソッドは、メソッド名の末尾に?を付ける慣習
```
def natural_number?(number)
  return number > 0
end

puts natural_number?(3) 
```


### bundle

* bundle install --path <フォルダ名>とすれば、指定のフォルダにインストール先を指定できる。指定先は`vendor/bundle`が一般的
* 一度上のコマンドを実行すれば、Bundlerはインストール先を記憶するので次回以降はbundle installを実行するだけで毎回vendor/bundleディレクトリにインストールされる
* railsアプリのルートディレクトリの下のvendor/bundleディレクトリにインストールされ、そのアプリケーションの中だけにインストールされる
* Bundlerはgemのインストール先を、railsアプリのルートディレクトリの下の、.bundle/configというファイルで記憶している

```
# gemのインストール
bundle install --path vendor/bundle
bundle update
```

```
# gemのインストール？

$ printf "install: -N \nupdate: -N\n" >> ~/.gemrc

$ gem install rails -v 5.1.6
# バージョンを指定して rails new
$ rails _5.1.6_ new mini_tweet_app
$ cd mini_tweet_app/

```

### エラー解消方

```
gem "better errors"
gem "binding of caller"

byebug # railsに恐らくデフォルトで入っている。import pdb;pdb.trace()のように仕込めばデバッグ可能
```

* エラー検索方法
  * `is:issue is:open キーワード` githubで検索する
  * スタックオーバーフローは✔がベストアンサー、しかし投票数が多いものの方が参考すべき

### インストール(AWS-amazonlinux)  -> [参考](https://qiita.com/Ekodhikodhi/items/01eab1b2b5785163e684)

* rbenvのインストール

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

* ruby-buildのインストール。
  `※Rubyをrbenv経由でインストールする時に必要なrbenvのプラグイン。これ入ってないとRubyインストール不可`

```
# リポジトリからクローン
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

# インストールの実行
cd ~/.rbenv/plugins/ruby-build
sudo ./install.sh

# インストール可能なRubyのバージョン一覧が表示されればruby-buildのインストールは成功
rbenv install -l
```

* Rubyのインストール

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
