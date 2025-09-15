# python

- [python](#python)
  - [インストール](#インストール)
  - [環境系](#環境系)
  - [基本系](#基本系)
    - [調査](#調査)
    - [整形](#整形)
    - [テスト系](#テスト系)
      - [unittest](#unittest)
      - [デバッグ](#デバッグ)
    - [よく使うライブラリ](#よく使うライブラリ)
      - [ログ](#ログ)

## インストール

- [バイナリ(直インストール):参考１](https://www.build-python-from-source.com/)
- [バイナリ(直インストール):参考２](https://docs.posit.co/resources/install-python-source/)

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

- [pyenv(バージョン管理パッケージ)のインストール](https://github.com/pyenv/pyenv#basic-github-checkout)

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

- pythonのバイナリインストール(ちょっと古い3.5.9の時)

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

- 仮想環境作成

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

- メモリのサイズを確認する

```python
sys.getsizeof(リストとか)
```

## 基本系

- 埋込出力

```python
# %sは文字列埋込、%dは整数、浮動小数が%f
print('合計 %s 個です' % len(list))

# format形式
print('{} is {} years old'.format(s, i))
```

- lambda

```python
def double(n):
    return n * 3

lambda_ver = lambda n: n * 3 # doubleと同じ

print(double(3) == lambda_ver(3)) # Trueとなる
```

- for文

```python
for i in range(5):
    print(i)
# 1行で出力
for x in my_list: print(x)
```

- デコレータ
  - 関数やメソッドに「特別な機能や処理」を簡単に追加するための記法・仕組み
    - ログ出力を自動化
    - 例外処理を共通化
    - 関数の前後で何かを実行（タイミング計測、認証、キャッシュ化など）

```python
def my_decorator(func):
    def wrapper(*args, **kwargs):
        print("処理前")
        result = func(*args, **kwargs)
        print("処理後")
        return result
    return wrapper

@my_decorator
def hello():
    print("こんにちは")

hello()

# 処理前
# こんにちは
# 処理後
```

- 非同期

```python
# 関数の頭にasyncとつけるとawaitを使った非同期処理が使える
async def sample():
    await asyncio.sleep(1)      # 非同期で1秒待つ
    return {"message": "Async after 1s"}
```

### 調査

- メソッドの一覧を取得する

```python
import inspect
obj = "test"
for x in inspect.getmembers(obj):
    print(x)
```

- pdbでメソッドの一覧を取得する

```pdb
obj.__dir__()
```

### 整形

- PEP8とは？
  - 「どこにスペースを入れるか」「インデントは何文字か」「関数名はどう付けるか」など、可読性の高いPythonコードを書くための公式スタイルガイドルール集

```sh
# インストール
pip install flake8 autopep8

# 指定のpythonファイルがPEP8に準拠しているか確認
flake8 ファイル名.py

# 指定のpythonファイルをPEP8に準拠するようコードを修正する
autopep8 --in-place ファイル名.py
# より積極的に修正する(aggressive1回指定で中程度、複数指定でさらに強力)
autopep8 --in-place --aggressive --aggressive ファイル名.py
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

- 処理速度計算

```python
import time
start = time.time()

# 処理の内容

end = time.time()
print(f"create_csvの処理時間: {end - start:.4f}秒") # .4f = 小数点下4桁まで表示
```

#### unittest

```python
mock.assert_called()      #呼び出されたかどうかを確認
mock.assert_called_once() #呼び出されたか回数が１度だけかどうかを確認
```

#### デバッグ

- vscode
  - venv環境のpythonを適用したい場合
    1. Ctl + Shift + P で`Python: Select interPereter`をクリック
    2. `Enter interpreter path...`を選択してvenvのパスを設定

### よく使うライブラリ

- subprocess
  - pythonからコマンドを実行

```python
import subprocess

subprocess.run(["ls", "-l"])
subprocess.run("ls -l", shell=True)
```

- pipreqs
  - Pythonプロジェクトの依存関係を自動的に検出し、requirements.txtファイルを生成するツール

```sh
pip install pipreqs
pipreqs .  # 現在のディレクトリに requirements.txt を自動生成
```

#### ログ

- 基本

```python
# ログの一括設定(ルートログ)
logging.basicConfig(
    level=logging.INFO,  # INFOレベル以上を出力
    format="%(asctime)s [%(levelname)s] %(message)s"
)

# ログの個別設定
logger = logging.getLogger()
logger.setLevel(logging.INFO)
```

- 応用
  - ログを`../log/log_file_name.log`に保存する
  - 日次でローテートする
    - 30世代分保管する
    - ローテートファイル名には`log_file_name.log.20250101`と命名する

```python
log_dir = os.path.abspath(os.path.join(os.path.dirname(__file__), '../log'))
os.makedirs(log_dir, exist_ok=True)
log_path = os.path.join(log_dir, 'log_file_name.log')
file_handler = TimedRotatingFileHandler(
    log_path, when='midnight', interval=1, backupCount=30, encoding='utf-8', utc=False
)
file_handler.suffix = "%Y%m%d"  # ローテート後のファイル名にYYYYMMDDを付与

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s %(levelname)s %(message)s',
    handlers=[
        file_handler,
        logging.StreamHandler()
    ]
)

- スタックトレースを出力

```python
    except Exception as error:
        import traceback
        logging.error(f"エラーが発生しました: {error}")
        logging.error(traceback.format_exc())
        raise
```
