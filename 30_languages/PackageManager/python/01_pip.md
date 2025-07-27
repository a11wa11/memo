# pip

## コマンド

### 確認

```sh
# インストール済パッケージを確認
pip list

# インストール済パッケージが最新版かどうか確認
pip list --outdated

# パッケージの詳細や依存関係(Requiresに表示される)を確認
pip show 対象パッケージ
```

### パッケージ保存

```sh
# 保存ファイル名は何でもOKだが、requirements.txtが一般的によく見る
pip freeze > requirements.txt
```

### インストール

```sh
# dry-run（実際にはインストールしない）
pip install --dry-run 対象パッケージ

# 基本インストールコマンド
pip install 対象パッケージ

# バージョンを指定。ダウングレードも可能
pip install 対象パッケージ==バージョン

# インストール可能なバージョンを表示。　※あえてバージョン指定しないことでインストール可能なバージョンが表示される
pip install ipython==

# インストールするディレクトリを指定
pip install -t 対象ディレクトリ 対象パッケージ

# 強制再インストール
pip install --force-reinstall 対象パッケージ

# キャッシュなしでインストール
pip install --no-cache-dir 対象パッケージ

# インターネットではなローカルの指定のパスからパッケージをインストールする
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock
pip install --no-index --find-links=/path/path/libraries -c path/requirements.txt
pip install --no-index --find-links=/path/path/libraries -c path/requirements.lock -r path/requirements.txt

# requirements.txtから一括インストール
pip install -r requirements.txt
```

### アンインストール

```sh
pip uninstall 対象パッケージ

# requirements.txtから一括アンインストール
pip uninstall -y -r requirements.txt
```

### アップグレード

```sh

pip install -U 対象パッケージ
pip install --upgrade 対象パッケージ
```

### 依存関係確認

- pipdeptree
  - 依存関係をツリー状で表示する
  - pipdeptreeは標準で入っていないため、要インストール

```sh
# インストール
pip install pipdeptree

# 全パッケージの依存関係をツリー状で表示
pipdeptree

# 指定のパッケージの依存関係を表示
pipdeptree -p 対象パッケージ
```

- pip-autoremove
  - pip-autoremoveは依存関係にあるパッケージをまとめて削除する
  - pip-autoremoveは標準で入っていないため、要インストール

```sh
# インストール
pip install pip-autoremove
# 依存関係にあるパッケージをまとめて削除
pip-autoremove 対象パッケージ
```
