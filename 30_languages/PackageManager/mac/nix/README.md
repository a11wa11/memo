# nix

## 概要

nixは2種類あり、Upstream NixとDeterminate Nixがある
- Upstream Nix(本家)
- Determinateは上位互換で、下流ディストリビューションとしてupstream Nixの全機能を含み、独自機能を提供

## インストール

```sh
# Determinate Nix(上位互換)
curl -fsSL https://install.determinate.systems/nix | sh -s -- install
# Upstream Nix(本家)
curl -sSfL https://artifacts.nixos.org/nix-installer | sh -s -- install --enable-flakes
```

### アンインストール

```sh
/nix/nix-installer uninstall
```


## 用語

| 用語 | 概要 | 補足1 | 補足2 |
| - | - | - | - |
| flake | nixはラテン語で「雪」、ロゴも雪の結晶。flakeはその一片という意味 |  |  |
| flake.nix | nix版のpackage.json |  |  |
| flake.lock | nix版のpackage-lock.json |  |  |
| home.nix | Home Managerに「自分のユーザー環境をこうしてほしい」と宣言する設定ファイル |  |  |
| darwin.nix | Mac側の設定を保存する|  |  |
| Home Manager | Nixを使って「自分のユーザー環境」を宣言的に管理するツール |  |  |
|  |  |  |  |
|  |  |  |  |

## コマンド

```sh
# PATH上に見つかるnix実行ファイルを、PATHの探索順にすべて表示(PATH検索でよく使う)
which -a nix
# パッケージインストール
nix profile add nixpkgs#パッケージ名
nix profile add nixpkgs#パッケージ名1 nixpkgs#パッケージ名2 # 複数インストール

# 以下の記述はどちらも同じ意味
nix profile add nixpkgs#act
nix profile add github:NixOS/nixpkgs/nixpkgs-unstable#act

# バージョン指定したいとき
nix shell nixpkgs#jq github:NixOS/nixpkgs/nixos-25.05#php83

# パッケージアンインストール
nix profile remove パッケージ名
# パッケージ一覧(インストール済)
nix profile list
# nixで扱えるパッケージを検索
nix search nixpkgs パッケージ名
# 指定パッケージをアップグレードする
nix profile upgrade パッケージ名
nix profile upgrade --all # 全パッケージをアップグレード
# flakeで使う名前のショートカット一覧を表示する
nix registry list

nix store gc
# profileの世代一覧と各段階での変更内容を表示
nix profile history
# profile を1つ前の世代に戻す。brewに相当機能はない
nix profile rollback
nix profile rollback --to 5 # 特定の世代へ移動
# 古い世代を消す
nix profile wipe-history --older-than 30d

# プロジェクト用の開発環境に入る。flake.nixのdevShellに書いた環境などが使えるようになる
nix develop
# init今いるディレクトリにflakeのひな形を作る
nix flake
# flake.lockを更新する。nodeでいうlockファイル更新に近い
nix flake update
# そのflakeが何を提供しているか確認する
nix flake show
# nodeなどを一時的に使えるシェル環境を作成して入る
nix shell nixpkgs#nodejs
# インストールせず、一時的にコマンドを実行する。試しに使いたいとき便利
nix run nixpkgs#nodejs
# flakeの属性を評価して中身を確認
nix eval nixpkgs#aws-sam-cli.version

# home.nixを作成
nix run home-manager/release-26.05 -- init --switch
nix run home-manager/release-26.05 -- switch --flake ~/.config/home-manager

# home.nixの状態を反映する
home-manager switch
```
