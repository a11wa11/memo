# composer

- [composer](#composer)
  - [インストール(mac)](#インストールmac)
  - [文法](#文法)
    - [パッケージインストール](#パッケージインストール)

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

### パッケージインストール

```sh
# 対象パッケージをインストール
composer install
```

## 設定

- composer.lockファイルは実際にインストールされたライブラリを記録する。
- composer.jsonはライブラリのバージョンをワイルドカードで指定することもできるため、composer.lockの内容と必ずしも一致しない
- vendorディレクトリにはインストールしたライブラリが保存される

- composer.jsonファイル記述例

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```
