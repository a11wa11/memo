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
