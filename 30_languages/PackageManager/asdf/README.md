# asdf

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
```

### asdfからパッケージマネージャーのインストール

- インストール可能なパッケージマネージャーを表示

```sh
asdf plugin list all
```

- パッケージマネージャーをインストール

```sh
asdf plugin add パッケージ名

asdf plugin add nodejs    # nodejs
asdf plugin add php       # php
asdf plugin add terraform # terraform
```

- 対象バージョンをインストール

```sh
asdf install パッケージ名 バージョン名

asdf install php 7.3.33
```

### アンインストール

- パッケージマネージャーをアンインストール

```sh
asdf plugin remove パッケージ名
```

- 対象バージョンをアンインストール

```sh
asdf uninstall パッケージ名 バージョン名

asdf uninstall php 7.3.33
```

### 確認

- インストールしたパッケージマネージャーの確認

```sh
# インストール済パッケージマネージャーのみ表示
asdf plugin list
# インストール済パッケージマネージャーとインストール済バージョンの表示
asdf list
```

- 対象パッケージマネージャーのインストール可能なバージョンを確認

```sh
asdf list all パッケージ名

asdf list all nodejs
asdf list all php
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

### 設定

- 使用バージョン設定

```sh
# どのディレクトリでも対象のバージョンを使うように設定
asdf global nodejs 18.19.0
# カレントディレクトリでのみ対象のバージョンを使うように設定
asdf local nodejs 18.19.0
# 一時的に対象のバージョンを使うように設定(ターミナルを閉じたり、新しいターミナルを開いたらリセットされる)
asdf shell nodejs 16.14.2
```
