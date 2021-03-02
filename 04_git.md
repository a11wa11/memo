# git

* 初期設定

```
git config --global user.name "test_name"
git config --global user.email "test_address"
```

* リモートリポジトリの設定

```
git remote add origin(任意のリモートリポジトリ名) git@github.com:hoge/test.git

# githubに既存ローカルリポジトリをプッシュする場合
git remote add origin git@github.com:hoge/test.git
git branch -M main
git push -u origin main
```

* リモートブランチをローカルブランチに強制的にもってくる（ローカルでコンフリクト発生して手に負えない時など）

```
git fetch origin ブランチ名
git reset --hard origin/ブランチ名
```

* リモートリポジトリのタグをローカルに強制的に持ってくる

```
git fetch --tags -f
```
