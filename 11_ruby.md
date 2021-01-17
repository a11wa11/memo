def natural_number?(number)
  return number > 0
end

puts natural_number?(3) 

## 真偽値を返すメソッドは、メソッド名の末尾に?を付ける慣習

```
# gemのインストール
bundle install
bundle update

# bundle install --path <フォルダ名>とすれば、指定のフォルダにインストール先を指定できる
# ailsアプリのルートディレクトリの下のvendor/bundleディレクトリにインストールされ、そのアプリケーションの中だけにインストールされる。
# 一度上のコマンドを実行すれば、Bundlerはインストール先を記憶するので次回以降はbundle installを実行するだけで毎回vendor/bundleディレクトリにインストールされる。
# 因みに、Bundlerはgemのインストール先を、railsアプリのルートディレクトリの下の、.bundle/configというファイルで記憶している。


bundle install --path vendor/bundle
```

```
# gemのインストール？

$ printf "install: -N \nupdate: -N\n" >> ~/.gemrc

$ gem install rails -v 5.1.6
# バージョンを指定して rails new
$ rails _5.1.6_ new mini_tweet_app
$ cd mini_tweet_app/