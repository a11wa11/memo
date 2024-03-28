# git

- [git](#git)
  - [設定](#設定)
  - [コミット関連](#コミット関連)
    - [リセット](#リセット)
    - [stash](#stash)
    - [チェリーピック](#チェリーピック)
  - [マージ関連](#マージ関連)
  - [ブランチ関連](#ブランチ関連)
    - [リモートリポジトリ設定](#リモートリポジトリ設定)
    - [フォーク](#フォーク)
  - [diff関連](#diff関連)
  - [タグ](#タグ)
  - [submodule](#submodule)
  - [github](#github)

## 設定

- グローバルユーザー設定(初期設定)

```sh
git config --global user.name "test_name"
git config --global user.email "test_address"
```

- ローカルユーザー設定

```sh
git config --local user.name "test_name"
git config --local user.email "test_address"
```

## コミット関連

```sh
# 直近のコミットメッセージの変更
git commit --amend -m "変更したいメッセージ"

# マージを打ち消したい時
git revert -m 1 コミットID

# マージする前に戻る
git merge --abort

# 以下の流れでやればmaster(main)ブランチが綺麗なまま余計なコミットをgit revertで取り消せる
git revert コミットID
git push origin HEAD
git merge リバートしたブランチ

# トピックブランチなど空のコミットを打ちたい時
git commit --allow-empty -m "# プルリクエストのタイトル"
```

### リセット

```sh
# addを取り消す
git reset HEAD addしたファイル名

# addを取り消す(直前のコミット時の状態に戻る)
git restore --staged ファイルパス

# 直前のコミット操作を取り消す
git reset --soft HEAD^

# reflogの履歴からもとに戻す
git reset --hard HEAD@[1]

# 特定のファイルだけ前の状態に戻したいとき
git checkout コミットハッシュ ファイルパス

# リモートブランチをローカルブランチに強制的にもってくる（ローカルでコンフリクト発生して手に負えない時など）
git fetch origin ブランチ名
git reset --hard origin/ブランチ名
```

### stash

```sh
# コミットしていない内容を一時退避(変更内容をコミットしないとブランチ変更できないのでそういう時に使う)
git stash

# 一時退避する内容を名前をつけて保存
git stash save "後で見るけど消してもいいstash"

# 一時退避したリストの一覧
git stash list

# 退避した内容をdiffで確認
git diff HEAD..stash@{0}

# 一時退避した内容(stash)をリストから指定して戻す
git stash pop stash@{0} # stash@{0}は適宜変更

# 一時退避した内容(stash)を一部消去
git stash drop stash@{0} # stash@{0}は適宜変更

# 一時退避した内容(stash)を全て消去
git stash clear
```

### チェリーピック

```sh
# チェリーピック(単体でも複数でも可)
git cherry-pick コミットID
git cherry-pick コミットID1 コミットID2 
git cherry-pick コミットID1..コミットID9  # 1~9をまとめてチェリーピック 

# メッセージを変更してチェリーピック
git cherry-pick -e コミットID

# 取り込みのみでコミットなし
git cherry-pick -n コミットID

# チェリーピックでマージコミットを対象とする時。 -m 1 がマージ先（左のコミットID）、-m 2 だとマージ元(右のコミットID)になるが、ほとんどのケースで-m 1 を使用する
git cherry-pick -m 1 コミットID


# こういう流れのgit log があるとして、
      X-Y
     /   \
...-A-B-C-D-...

# Dはマージコミット。親１がX,Yを、親2がB,Cのコミットを持つので
git cherry-pick X Y  =  git cherry-pick -m 1 D
git cherry-pick B C  =  git cherry-pick -m 2 D
```

## [マージ関連](https://qiita.com/horimislime/items/84fa431460c8d39f37e6)

- コンフリクトされるか確認したい時

```sh
# コミットされないが、ローカルファイルはマージされてしまう
git merge ブランチ名 --no-commit

(トピックブランチ) git format-patch master --stdout > test.patch
(トピックブランチ) git checkout master
(master) git apply test.patch --check # パッチが適用できるかチェック
# コンフリクトが起きる場合はエラーが発生する
```

- git log　表記で見るコミットハッシュ
  - yyyy => マージされるブランチ
  - zzzz => マージするブランチ
  - yyyyブランチにいて `git merge zzzz`して出来上がったブランチのコミットハッシュがXXXXとなる

```sh
commit XXXX (HEAD -> master, upstream/master)
Merge: yyyy zzzz
Author: sample <sample@users.noreply.github.com>
```

## ブランチ関連

```sh
# 変更したいブランチにいる時
git branch -m 新ブランチ名

# ローカルブランチの設定変更(別ブランチにいる時)
git branch -m 古いブランチ名 新しいブランチ名

# 任意のブランチ名をつけてチェックアウト
git checkout -b ブランチ名 origin/リモートブランチ名

# ブランチを削除する
git branch -d ブランチ名

# ブランチを強制削除
git branch -D ブランチ名

# リモートブランチのIPアドレス変更
git remote set-url origin git@0.0.0.0:*****.git

# リモートブランチの最新状態を取得（リモートブランチの削除も同期）
git fetch -p

# リモートで削除されているブランチをローカルでも削除する
git fetch --prune
```

### リモートリポジトリ設定

```sh
git remote add origin(任意のリモートリポジトリ名) git@github.com:hoge/test.git

# githubに既存ローカルリポジトリをプッシュする場合
git remote add origin git@github.com:hoge/test.git
git branch -M main
git push -u origin main
```

- 対象のローカルブランチをリモートブランチに強制プッシュ

```sh
git push -f origin ローカルブランチ:リモートブランチ
```

- 特定の鍵を指定してリモートリポジトリに接続

```sh
GIT_SSH_COMMAND='ssh -i ~/.ssh/id_rsa' git clone リモートリポジトリ
```

### フォーク

```sh
# フォーク元をリモートリポジトリに追加
git remote add upstream git@github.com:XXXXXXXXXXXXXXXX

# フォーク元からpull
git pull upstream ブランチ名

# フォーク元のPRにチェックアウト
git fetch upstream pull/ID番号/head:pr/ID番号
git checkout pr/ID番号

# フォーク元の最新状態をリモート先に反映
git pull upstream ブランチ名
git push origin ブランチ名

# フォーク先の未マージのブランチを使用したい時
git fetch upstream pull/<ID>/head:pr/<ID>
git checkout pr/<ID>
```

## diff関連

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

## タグ

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

## submodule

- `.gitmodules`ファイルでサブモジュールの設定情報が管理されている

```sh
# 追加
git submodule add クローン元 追加先パス
# バージョン管理して追加
git submodule add --branch ブランチ名 クローン元 追加先パス
# 初期化
git submodule init
# 更新
git submodule update
git submodule update 指定サブモジュールパス # 特定のサブモジュールのみの時
# 確認
git submodule status
```

## github

Personal access tokens

- GitHubのリモートリポジトリにHTTP接続する際に、ユーザ名と合わせて必要となる情報

[トークンの作成手順](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)

```sh
# Personal access tokens発行後に下記のようにして使用する
$ git clone https://github.com/USERNAME/REPO.git
Username: YOUR_USERNAME
Password: YOUR_PERSONAL_ACCESS_TOKEN
```

ブランチやコミット同士の比較

- `https://github.com/リポジトリ/compare/stg...dev`のように`compare/ブランチ比較元...ブランチ比較先`のように打つ
