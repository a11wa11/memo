# asdf

- [asdf](#asdf)
  - [コマンド](#コマンド)
    - [asdfのインストール](#asdfのインストール)
    - [パッケージマネージャー管理](#パッケージマネージャー管理)
    - [言語確認](#言語確認)
    - [言語インストール](#言語インストール)
    - [言語アンインストール](#言語アンインストール)
    - [言語設定](#言語設定)
  - [ドキュメント](#ドキュメント)

[公式ガイド](https://asdf-vm.com/guide/getting-started.html#_1-install-dependencies)

## コマンド

### asdfのインストール

- brewでインストール

```sh
# mac
brew install asdf
```

- [gitでインストール](https://asdf-vm.com/guide/getting-started.html)

```sh
# gitでインストール
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.1

# bashの場合
# asdf必要な環境変数を設定
. "$HOME/.asdf/asdf.sh"
. "$HOME/.asdf/completions/asdf.bash"
# PATHを通す場合
export PATH="$HOME/.asdf/shims:$PATH"


# 最近のv16以降は手順が違う https://asdf-vm.com/guide/getting-started.html
## 予めgoをインストール
dnf install -y golang    # amazonlinux2023の場合
apt install -y golang-go # ubuntuの場合
git clone https://github.com/asdf-vm/asdf.git --branch v0.18.0
cd asdf
make
cp asdf(makeでできるasdfのバイナリパス) /usr/local/bin(通したいパス)

asdf # asdfのバージョンが表示されれば成功
```

- dockerでのインストール手順

```sh
```

### パッケージマネージャー管理

- インストール可能なパッケージマネージャーを表示

```sh
asdf plugin list all
```

- パッケージマネージャーの確認

```sh
# インストール済パッケージマネージャーのみ表示
asdf plugin list
```

- パッケージマネージャーをインストール

```sh
asdf plugin add パッケージ名

asdf plugin add python    # python
asdf plugin add nodejs    # nodejs
asdf plugin add php       # php
asdf plugin add terraform # terraform
```

- パッケージマネージャーをアンインストール

```sh
asdf plugin remove パッケージ名
```

- パッケージの更新

```sh
asdf plugin update golang
asdf plugin-update nodejs
```

### 言語確認

- インストールしたパッケージマネージャーの確認

```sh
# インストール済パッケージマネージャーとインストール済バージョンの表示
asdf list
asdf list nodejs # 個別のパッケージマネージャーのインストール済バージョンの表示
```

- 対象パッケージマネージャーのインストール可能なバージョンを確認

```sh
asdf list all パッケージ名

asdf list all python
asdf list all nodejs
asdf list all php
asdf list-all golang # list-allでも可能
```

- 対象パッケージマネージャーの最新バージョンを確認

```sh
asdf latest パッケージ名

asdf latest nodejs

# インストール済パッケージマネージャーの最新バージョンを表示
asdf latest --all
```

- 設定されたバージョンを確認

```sh
asdf current # 全体

asdf current 対象パッケージ名
asdf current nodejs
```

- インストールされているパスを表示

```sh
asdf where nodejs
asdf where golang
```

### 言語インストール

- 対象バージョンをインストール

```sh
asdf install パッケージ名 バージョン名

asdf install python 3.13.5
asdf install php 7.3.33
```

### 言語アンインストール

- 対象バージョンをアンインストール

```sh
asdf uninstall パッケージ名 バージョン名

asdf uninstall php 7.3.33
```

### 言語設定

- 使用バージョン設定

```sh
# どのディレクトリでも対象のバージョンを使うように設定
asdf global nodejs 18.19.0
asdf set --global python 3.13.5 # asdf v0.10.0 以降は asdf global コマンドは廃止され、asdf set に統一

# カレントディレクトリでのみ対象のバージョンを使うように設定
asdf local nodejs 18.19.0
asdf set python 3.13.5 # 現在のディレクトリの.tool-versionsに反映
# 一時的に対象のバージョンを使うように設定(ターミナルを閉じたり、新しいターミナルを開いたらリセットされる)
asdf shell nodejs 16.14.2
```

## ドキュメント

- [github](https://github.com/asdf-vm/asdf)
