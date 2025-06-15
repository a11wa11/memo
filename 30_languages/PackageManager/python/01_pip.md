# pip

## コマンド

### インストール

```sh
# dry-run（実際にはインストールしない）
pip install --dry-run 対象パッケージ

# 基本インストールコマンド
pip install 対象パッケージ

# バージョンを指定。ダウングレードも可能
pip install ライブラリ名==バージョン

# インストール可能なバージョンを表示。　※あえてバージョン指定しないことでインストール可能なバージョンが表示される
pip install ipython==

# インターネットではなローカルの指定のパスからライブラリをインストールする
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock
pip install --no-index --find-links=/path/path/libraries -c path/requirements.txt
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock -r path/requirements.txt
```

### アンインストール

```sh
pip uninstall ライブラリ名
```

### アップグレード

```sh

pip install -U ライブラリ名
pip install --upgrade ライブラリ名
```

### 確認

```sh
# インストール済パッケージを確認
pip list

# インストール済パッケージが最新版かどうか確認
pip list --outdated

# パッケージの詳細や依存関係(Requiresに表示される)を確認
pip show ライブラリ名
```

### パッケージの保存

```sh
# 保存ファイル名は何でもOKだが、requirements.txtが一般的によく見る
pip freeze > requirements.txt

# 保存したライブラリ名をrequirements.txtからインストール
pip install -r requirements.txt
```

### 依存関係確認

- pipdeptree
  - 依存関係をツリー状で表示する
  - pipdeptreeは標準で入っていないため、要インストール

```sh
# インストール
pip install pipdeptree

# 全ライブラリの依存関係をツリー状で表示
pipdeptree

# 指定のライブラリの依存関係を表示
pipdeptree -p ライブラリ名
```

- pip-autoremove
  - pip-autoremoveは依存関係にあるライブラリをまとめて削除する
  - pip-autoremoveは標準で入っていないため、要インストール

```sh
# インストール
pip install pipdeptree
# 依存関係にあるライブラリをまとめて削除
pip-autoremove ライブラリ名
```
