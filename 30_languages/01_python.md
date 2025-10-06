# python

- [python](#python)
  - [インストール](#インストール)
  - [環境系](#環境系)
  - [基本系](#基本系)
    - [実行オプション](#実行オプション)
  - [整形](#整形)
  - [よく使うライブラリ](#よく使うライブラリ)
  - [テスト系](#テスト系)
    - [unittest](#unittest)
  - [ログ](#ログ)
    - [構造](#構造)
  - [デバッグ](#デバッグ)
    - [調査](#調査)

## インストール

- [バイナリ(直インストール):参考１](https://www.build-python-from-source.com/)
- [バイナリ(直インストール):参考２](https://docs.posit.co/resources/install-python-source/)

<details>
    <summary>python直インストール</summary>

- Dockerでのamazonlinux2イメージでのpythonインストール

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

</details>

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

- 辞書

```python
event.get("test", [])  # 第2引数は取得できなかった場合の値
```

- for文
  - pythonにはcase文はない

```python
for i in range(5):
    print(i)
# 1行で出力
for x in my_list: print(x)
```

- リスト内包表記

```python
event_name = [event.get("name") for event in events]
```

- lambda

```python
def double(n):
    return n * 3

lambda_ver = lambda n: n * 3 # doubleと同じ

print(double(3) == lambda_ver(3)) # Trueとなる
```

- スライス記法
  - `[開始位置:終了位置]`の形式で抽出可能

```python
# 文字列の場合
alphabet = "ABCDEFGHIJ"
var = alphabet[:5]  # 結果: "ABCDE"

# リストの場合
list_var = [10, 20, 30, 40, 50, 60, 70]
var = list_var[:5]  # 結果: [10, 20, 30, 40, 50]
```

- 辞書アンパック
  - 動的に引数を変更できる

```python
config = {
    'region_name': 'ap-northeast-1',
    'aws_access_key_id': 'dummy',
    'aws_secret_access_key': 'dummy'
}

# **configで辞書をアンパック
boto3.resource('dynamodb', **config)
# ↓これと同じ意味
boto3.resource('dynamodb', 
               region_name='ap-northeast-1',
               aws_access_key_id='dummy',
               aws_secret_access_key='dummy')
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

### 実行オプション

```python
PYTHONDONTWRITEBYTECODE=1 python スクリプト名.py
```

## 整形

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

## よく使うライブラリ

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

## テスト系

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

### unittest

```python
mock.assert_called()      #呼び出されたかどうかを確認
mock.assert_called_once() #呼び出されたか回数が１度だけかどうかを確認
```

## ログ

### 構造

- ロガー: 誰がログを出力するか(ルートか個別の名前のログかなど)の窓口
  - ログレベル: どの重要度以上ならログを吐くかという基準
  - ハンドラー: どこで出力するか(標準出力かファイル化などかなど)
    - フォーマッター: どの様な形式でログを出力するか

- ルートロガー
  - `logging.basicConfig`で一括設定。一度のみの設定
  - 設定は子ロガーにも引き継がれる
- 個別ロガー
  - ルートロガーの設定引き継ぎ
  - 個別の設定更新可能

- ルートロガー基本設定

```python
# ログの一括設定(ルートログ)
logging.basicConfig(
    level=logging.INFO,  # INFOレベル以上を出力
    format="%(asctime)s %(name)s [%(levelname)s] %(message)s"
    # デフォルトは%(levelname)s:%(name)s:%(message)s
)

# ロガー取得(引数なしならルートロガーとなる)
logger = logging.getLogger()
```

- 応用1(ローカルサーバーで長時間利用時など)
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
    format="%(asctime)s %(name)s [%(levelname)s] %(message)s"
    # ハンドラーを複数(ファイルと標準出力)設定
    handlers=[
        file_handler,
        logging.StreamHandler()
    ]
)
```

- 応用2(サーバレスなどファイル保存不可の場合など)

```python
# ローカル出力用
logging.basicConfig(
    level=logging.INFO,  # INFOレベル以上を出力
    format="%(asctime)s [%(levelname)s] %(message)s"
)

# logging環境起動(AWS Cloudwatch書き込み用)
logger = logging.getLogger("app")

# JSTタイムゾーンのconverterを追加
formatter = logging.Formatter(
    fmt="%(asctime)s %(name)s [%(levelname)s] %(message)s",
    datefmt="%Y-%m-%d %H:%M:%S"
)
formatter.converter = lambda *args: datetime.datetime.now(
    ZoneInfo("Asia/Tokyo")).timetuple()

# Lambdaでは既存のハンドラ（handler）があるので、全てのhandlerに適用
for handler in logger.handlers:
    handler.setFormatter(formatter)
```

- スタックトレースを出力

```python
    except Exception as error:
        import traceback
        logging.error(f"エラーが発生しました: {error}")
        logging.error(traceback.format_exc())
        raise
```

## デバッグ

- vscode
  - venv環境のpythonを適用したい場合
    1. Ctl + Shift + P で`Python: Select interPereter`をクリック
    2. `Enter interpreter path...`を選択してvenvのパスを設定

- pdbでメソッドの一覧を取得する

```pdb
obj.__dir__()
```

### 調査

- メソッドの一覧を取得する

```python
import inspect
obj = "test"
for x in inspect.getmembers(obj):
    print(x)
```
