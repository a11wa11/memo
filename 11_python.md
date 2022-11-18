# python

## インストール

* [バイナリ(直インストール):参考１](https://www.build-python-from-source.com/)
* [バイナリ(直インストール):参考２](https://docs.posit.co/resources/install-python-source/)


<details>
    <summary>Dockerでのamazonlinux2イメージでのpythonインストール</summary>

```Dokerfile
FROM amazonlinux:2

WORKDIR /root

RUN yum install -y glibc-langpack-ja && yum clean all; \
    yum update -y && yum -y groupinstall "Development Tools" && \
    echo "alias ll='ls -la --color'" >> /root/.bashrc;

ENV LANG=ja_JP.UTF-8   \
    LANGUAGE=ja_JP:ja  \
    LC_ALL=ja_JP.UTF-8 \
    TZ=Asia/Tokyo

# install python
ARG PYTHON_VERSION="3.9.14"
RUN yum -y install wget gcc openssl-devel bzip2-devel libffi-devel xz-devel tk-devel && \
    cd /tmp/ && \
    wget https://www.python.org/ftp/python/${PYTHON_VERSION}/Python-${PYTHON_VERSION}.tgz && \
    tar xzf Python-${PYTHON_VERSION}.tgz && \
    cd Python-${PYTHON_VERSION} && \
    ./configure --prefix=/opt/python/${PYTHON_VERSION}/ --enable-optimizations --with-lto --with-computed-gotos --with-system-ffi && \
    make -j "$(nproc)" && \
    make altinstall && \
    rm /tmp/Python-${PYTHON_VERSION}.tgz && \
    PYTHON_VERSION_NUNBERS=(${PYTHON_VERSION//./ }) && \
    PYTHON_MAJOR=${PYTHON_VERSION_NUNBERS[0]} && \
    PYTHON_MINOR=${PYTHON_VERSION_NUNBERS[1]} && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR}.${PYTHON_MINOR}        /opt/python/${PYTHON_VERSION}/bin/python && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR}.${PYTHON_MINOR}        /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR} && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR}.${PYTHON_MINOR}           /opt/python/${PYTHON_VERSION}/bin/pip && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR}.${PYTHON_MINOR}           /opt/python/${PYTHON_VERSION}/bin/pip${PYTHON_MAJOR} && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/pydoc${PYTHON_MAJOR}.${PYTHON_MINOR}         /opt/python/${PYTHON_VERSION}/bin/pydoc && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/idle${PYTHON_MAJOR}.${PYTHON_MINOR}          /opt/python/${PYTHON_VERSION}/bin/idle && \
    ln -s /opt/python/${PYTHON_VERSION}/bin/python${PYTHON_MAJOR}.${PYTHON_MINOR}-config /opt/python/${PYTHON_VERSION}/bin/python-config && \
    echo "PATH=/opt/python/${PYTHON_VERSION}/bin/:$PATH" >> /root/.bashrc && source /root/.bashrc && \
    pip3 install boto3;
```
</details>

* [pyenv(バージョン管理パッケージ)のインストール](https://github.com/pyenv/pyenv#basic-github-checkout)

```sh
# linux、bashrcを使用する場合
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
cd ~/.pyenv && src/configure && make -C src
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec "$SHELL"
pyenv install 任意のバージョン
pyenv global 任意のバージョン
```

## 環境系

* 仮想環境作成

```sh
# 仮想環境構築
python -m venv 仮想環境名

# 仮想環境実行
source 仮想環境名/bin/activate

# 仮想環境停止
deactivate

# 仮想環境削除
rm -rf 仮想環境名
```

* メモリのサイズを確認する

```python
sys.getsizeof(リストとか)
```

## 基本系

```python
def double(n):
    return n * 3

lambda_ver = lambda n: n * 3 # doubleと同じ

print(double(3) == lambda_ver(3)) # Trueとなる
```

### 調査

* メソッドの一覧を取得する

```python
import inspect
obj = "test"
for x in inspect.getmembers(obj):
    print(x)
```

* pdbでメソッドの一覧を取得する

```pdb
obj.__dir__()
```

### テスト系

```sh
# 全体テスト
coverage run -m unittest discover

# 個別テスト
python -m unittest -v tests/test_sample.py
coverage run -m unittest -v tests/test_sample.py

# 結果出力
coverage report -m sample.py
coverage html
```

#### unittest

```python
mock.assert_called()      #呼び出されたかどうかを確認
mock.assert_called_once() #呼び出されたか回数が１度だけかどうかを確認
```


### パッケージ管理

* pip

```sh
# インターネットではなローカルの指定のパスからライブラリをインストールする
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock
pip install --no-index --find-links=/path/path/libraries -c path/requirements.txt
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock -r path/reuirements.txt
```

* [poetry](https://zenn.dev/canonrock/articles/poetry_basics)

```sh
# poetryの導入・pyproject.tomlの作成や対話式でプロジェクトに関する情報を決定する
poetry init

# パッケージ追加
poetry add パッケージ名
# オプションや引数指定例
poetry add パッケージ名 --dry-run # インストールされる内容を確認
poetry add "パッケージ名>1.15"    # 1.15より大きいバージョンの指定
poetry add "パッケージ名@latest"  # 最新のバージョンをインストール
# バージョン指定について
"^3.8" #3.8以上4.0より小さいバージョンという意味。3.9も含んでいる
"^3.8,<3.9" #3.8のみ指定し、3.9は含まず
"=3.8"      #3.8のみ指定し、3.9は含まず。上記と同じ

# パッケージの削除
poetry remove パッケージ名

# パッケージの確認
poetry show
# >> オプション例
poetry show --tree         # ツリー形式で依存関係を表示
poetry show --latest       # 最新バージョンを表示
poetry show -o(--outdated) # 古くなっているパッケージを表示

# パッケージ検索
poetry search

# パッケージの更新
poetry update パッケージ名 # 個別更新
poetry update            # 前パッケージの更新
poetry update --dry-run  # アップグレードされるパッケージを確認
# poetry自身の更新
poetry self update

# poetry.lockの生成
poetry lock

# 仮想環境からコマンド実行
poetry shell
poetry run python -m unittest ~~~

# pyproject.tomlの検証
portry check

# pyproject.tomlから一括インストール。poetry.lockがあればそれを参考にする
poetry install

# 環境関連
poetry env info             # 現在の仮想環境の情報
poetry env list             # 仮想環境一覧
poetry env remove <python>  # 仮想環境の削除
poetry env use <python>     # 仮想環境を作成、アクティベート

# pip freeze をpoetryで実行したい時
poetry run pip --disable-pip-version-check list --format=freeze
```

### よく使うライブラリ

* subprocess -> pythonからコマンドを実行

```python
import subprocess

subprocess.run(["ls", "-l"])
subprocess.run("ls -l", shell=True)
```
