# composer

- [composer](#composer)
  - [インストール(mac)](#インストールmac)
  - [文法](#文法)
    - [パッケージ操作](#パッケージ操作)
  - [設定](#設定)

## インストール(mac)

- コマンドインストール

```sh
# mac brewから
brew install composer
```

- [公式手順](https://getcomposer.org/download/)

```sh
# 公式手順
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
# パスの変更
sudo mv composer.phar /usr/local/bin/composer
```

## [文法](https://zenn.dev/hedrall/articles/251441f391990f)

```sh
# composerのバージョンを指定したバージョンへ更新する(ダウングレードも可能)
composer self-update 1.9.1(指定したバージョン)

# laravelのインストール
composer create-project --prefer-dist laravel/laravel プロジェクト名 "6.0.*(バージョン名)"
```

### パッケージ操作

- インストール

```sh
# 対象パッケージをインストール
composer install
composer install パッケージ名

# composer.json に必要なパッケージを追加してインストール
composer require パッケージ名
```

- デバッグ

```sh
# 実際には実行せず
composer update --dry-run
```

- アップデート

```sh
composer update
composer update パッケージ名
# 指定したパッケージから依存しているパッケージも一緒に更新
composer update --with-dependencies パッケージ名
```

- アンインストール

```sh
composer remove パッケージ名
# 指定したパッケージから依存しているパッケージも一緒に更新
composer remove --update-with-dependencies パッケージ名
# composer.json を修正するのみ
composer remove --no-update パッケージ名
```

- 調査

```sh
# パッケージ詳細
composer show パッケージ名
# 依存関係を表示
composer show -t
# パッケージ調査
composer search パッケージ名
# composer.json ファイルの構文と内容が正しいかどうかを検証
composer validate
# 指定パッケージのインストールを妨げるパッケージを表示する
composer why-not パッケージ名 バージョン
```

- パッケージキャッシュクリア

```sh
composer clear-cache
```

## 設定

- composer.lockファイルは実際にインストールされたライブラリを記録する。
- composer.jsonはライブラリのバージョンをワイルドカードで指定することもできるため、composer.lockの内容と必ずしも一致しない
- vendorディレクトリにはインストールしたライブラリが保存される

- composer.jsonファイル記述例

```json
{
    "require": {
        "monolog/monolog": "1.0.*",
        "google/apiclient-services": "~0.200", # 最小バージョンが200でメジャーバージョンは同一という意味
        "google/apiclient-services": "^0.200", # 最小バージョンが200でメジャーバージョンは同一という意味

    }
}
```
