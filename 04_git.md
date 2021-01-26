# git

* 初期設定

```sh
git config --global user.name "test_name"
git config --global user.email "test_address"
```

* リモートリポジトリの設定

```sh
git remote add origin(任意のリモートリポジトリ名) git@github.com:hoge/test.git

# githubに既存ローカルリポジトリをプッシュする場合
git remote add origin git@github.com:hoge/test.git
git branch -M main
git push -u origin main
```

* リモートブランチをローカルブランチに強制的にもってくる（ローカルでコンフリクト発生して手に負えない時など）

```sh
git fetch origin ブランチ名
git reset --hard origin/ブランチ名
```

* タグ関連

```sh
# リモートブランチのタグの一覧
git ls-remote --tags

# リモートリポジトリのタグをローカルに強制的に持ってくる
git fetch --tags -f

# タグへのチェックアウト
git checkout -b v.1.0.0 refs/tags/v1.0.0

# タグを消す
git tag -d タグ名

# リモートタグを消去
git push origin タグ名
```

* コミット編集

```sh
# 直近のコミットメッセージの変更
git commit --amend -m "変更したいメッセージ"

# チェリーピック
git cherry-pick コミットID

# マージを打ち消したい時
git revert -m 1 コミットID

# 以下の流れでやればmaster(main)ブランチが綺麗なまま余計なコミットをgit revertで取り消せる
git revert コミットID
git push origin HEAD
git merge リバートしたブランチ
```

* ブランチ関連

```sh
# ローカルブランチの設定変更
git branch -m 古いブランチ名 新しいブランチ名

# 任意のブランチ名をつけてチェックアウト
git checkout -b ブランチ名 origin/リモートブランチ名

# リモートブランチのIPアドレス変更
git remote set-url origin git@0.0.0.0:*****.git
```

* git diff

```sh
# ２つのブランチの差分を確認
git diff branch1 branch2

# ２つのブランチの指定のファイルの差分を確認
git diff branch1 branch2 sample.txt

# ２つのブランチの差分のファイル名のみ表示
git diff --name-only branch1 branch2

# 特定のブランチ間やタグ間のログを指定の形式で出力する
git log branch1..branch2 --pretty=oneline --pretty=format:"%n - %ad : %s" --date-order

```

* [マージ関連](https://qiita.com/horimislime/items/84fa431460c8d39f37e6)

```sh
# コミットされないが、ローカルファイルはマージされてしまう
git merge ブランチ名 --no-commit

(トピックブランチ) git format-patch master --stdout > test.patch
(トピックブランチ) git checkout master
(master) git apply test.patch --check # パッチが適用できるかチェック
# コンフリクトが起きる場合はエラーが発生する
```
