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

* pythonのバイナリインストール(ちょっと古い3.5.9の時)

```sh
wget https://www.python.org/ftp/python/3.5.9/Python-3.5.9.tar.xz
tar -Jxvf Python-3.5.9.tar.xz
Python-3.5.9/configure --prefix=/usr/local/python
make
make install
rm -rf /root/*
ln -s /usr/local/python/bin/python3.5 /usr/local/bin/python
ln -s /usr/local/python/bin/pip3.5 /usr/local/bin/pip
echo "export PATH=$PATH:/usr/local/python/bin" >> ~/.bashrc
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

* 埋込出力

```python
# %sは文字列埋込、%dは整数、浮動小数が%f
print('合計 %s 個です' % len(list))

# format形式
print('{} is {} years old'.format(s, i))
```

* lambda

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

### よく使うライブラリ

* subprocess -> pythonからコマンドを実行

```python
import subprocess

subprocess.run(["ls", "-l"])
subprocess.run("ls -l", shell=True)
```
