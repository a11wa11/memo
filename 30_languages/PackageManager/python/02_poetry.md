# poetry

[参考](https://zenn.dev/canonrock/articles/poetry_basics)

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
poetry check

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
