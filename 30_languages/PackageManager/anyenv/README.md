# anyenv

## インストール

```sh
# mac
brew install anyenv
```

## パッケージマネージャーのインストール

- インストール可能なパッケージマネージャーを表示

```sh
anyenv install -l
```

- パッケージマネージャーをインストール

```sh
anyenv install goenv(パッケージマネージャー名)

# M1 Macでanyenvでgoenvをインストールした場合にPATHを通す
echo 'export GOENV_ROOT="$HOME/.anyenv/envs/goenv"' >> ~/.zshrc
echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(goenv init -)"' >> ~/.zshrc
source ~/.zshrc

# bashrcの場合
echo 'export GOENV_ROOT="$HOME/.anyenv/envs/goenv"' >> ~/.bashrc
echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(goenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

### phpenv

- phpenvでPHPをインストールする場合に環境変数を設定してインストールする場合あり

```sh
PKG_CONFIG_PATH="$(brew --prefix icu4c@74)/lib/pkgconfig" CONFIGURE_OPTS="--with-bz2=$(brew --prefix bzip2) --with-iconv=$(brew --prefix libiconv) --with-tidy=$(brew --prefix tidy-html5)" phpenv install -v 8.1.29
```
