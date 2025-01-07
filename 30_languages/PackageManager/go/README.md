# goenv

## インストール

```sh
# mac
brew install goenv

# git
git clone https://github.com/syndbg/goenv.git ~/.goenv
```

## パッケージマネージャーのインストール

- インストール可能なパッケージマネージャーを表示

```sh
goenv install -l
```

- パッケージマネージャーをインストール

- ※Go言語はバージョン1.16からmacOSのARM64（Apple Silicon、通称M1チップ）を[公式にサポート](https://go.dev/dl/)。それ以前のバージョンはサポート外

```sh
goenv install 1.16(任意のバージョン)

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

### 参考

[goenv](https://tech.librastudio.co.jp/entry/2021/07/23/153304)
