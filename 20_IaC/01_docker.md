# docker

- [docker](#docker)
  - [インストール](#インストール)
  - [コマンド](#コマンド)
    - [設定](#設定)
    - [イメージ関連](#イメージ関連)
      - [よく使うオプション](#よく使うオプション)
      - [commit](#commit)
      - [push](#push)
    - [コンテナ関連](#コンテナ関連)
    - [ネットワーク関連](#ネットワーク関連)
    - [調査](#調査)
    - [整理](#整理)
  - [Dockerfile](#dockerfile)
  - [マルチCPUアーキテクチャ](#マルチcpuアーキテクチャ)
    - [docker buildx](#docker-buildx)
  - [Docker for Mac](#docker-for-mac)
  - [docker-compose](#docker-compose)

## インストール

- mac
  - `brew install docker`
- [centos7へdockerのインストール](https://qiita.com/kichise/items/f8e56c6d2d08eaf4a6a0)

- amazonlinux2023

```sh
#!/bin/bash
dnf update -y

# install docker
dnf install -y docker git
systemctl enable docker
systemctl start docker
usermod -aG docker ec2-user

# install docker-compose
curl -L https://github.com/docker/compose/releases/download/1.6.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## コマンド

### 設定

```sh
# 現在のDockerエンジンの状態や設定情報を確認
docker info

# Dockerエンジン（例: ローカルの Docker、リモートの Docker サーバ、Rancher Desktop 経由の Docker など）を確認
docker context ls

## Dockerエンジン切替
docker context use エンジン名

# DOCKER_HOST -> Docker CLIがどのホスト（およびポート）に接続してDockerデーモンと通信するかを指定するための環境変数
## デフォルトのDockerエンジンの場合
DOCKER_HOST=unix:///var/run/docker.sock
## rancher-desktopのDockerエンジンの場合
DOCKER_HOST=unix://$HOME/.rd/docker.sock

## rancher-desktopなどの場合、ソケットを指定して切り替える
DOCKER_HOST=unix://$HOME/.rd/docker.sock docker info
## もしくはシンボリックリンクを貼る(管理者権限必要かも)
ln -s $HOME/.rd/docker.sock /var/run/docker.sock
```

### イメージ関連

- イメージ確認

```sh
docker images
```

- イメージ検索

```sh
docker search イメージ名
```

- イメージ取得

```sh
docker pull イメージ名
```

- DockerFileからイメージを作成する

```sh
# イメージ作成基本コマンド。この場合イメージ名はランダムにつけられる
docker build (DockerFileのある)パス

# イメージ名を独自につける
docker build -t 名付けたいイメージ名 .(Dockerfileのあるパス)

# Dockerfileを指定してbuild
docker build -f Dockerfile_demo(Dockerfile以外の名前の場合) .(Dockerfileのあるパス)

# 鍵などシークレットを定義してbuild
docker build --secret id=任意のID名,src=対象のパス  -t 名付けたいイメージ名 .(Dockerfileのあるパス)
docker build --secret id=ssh_key,src=${HOME}/.ssh/my-key_rsa  -t 名付けたいイメージ名 .(Dockerfileのあるパス)

# マルチステージビルドで定義されたイメージを指定してbuild
docker build --target 定義されたイメージ -t 名付けたいイメージ名 .(Dockerfileのあるパス)

# ビルド時に失敗したコンテナや不要な中間コンテナを削除する
docker build --force-rm=true -t 名付けたいイメージ名 .(Dockerfileのあるパス)

# イメージ名とタグを独自につける
docker build -t 名付けたいイメージ名:タグ (DockerFileのある)パス

# 例
docker build -t sample:1 .
・
・
・
# 省略
Successfully built 1234abcdefgh ←作成されたイメージID
Successfully tagged sample:1  ←イメージ名とタグがつく。オプションつけなければこの表記されない
```

- dockerイメージの作成履歴を確認

```sh
docker history イメージ名
```

- イメージを削除する

```sh
# 対象を指定する際はイメージ名、イメージ名:タグ、イメージIDのどれかで指定する
docker rmi イメージ名
docker rmi イメージ名:タグ
docker rmi イメージID
```

- イメージを一括削除

```sh
# イメージIDのみを一覧表示
docker images -q
# イメージ一括削除
docker rmi -f `docker images -q`
# 不使用イメージ一括削除
docker image prune -f
```

#### よく使うオプション

- 変数指定

```sh
docker build -t イメージ名 --build-arg AVERSION(指定したい変数名)=${入力したい変数名値} .
```

- デタッチ `-d,--detach` ※バックグラウンドで起動
- 特権付与 `--privileged` ※systedなど使用したい時
- 自動削除 `--rm`
- マウント `-v ローカルホスト側パス:コンテナ側パス`

```sh
docker run -d --privileged --rm -v $(pwd):/home/ec2-user/workdir --name コンテナ名 イメージ名
```

- ポート開放 `-p 8080:8080`

```sh
docker run -it -p 8080:8080 イメージ名
```

#### commit

- コンテナからイメージを作成

```sh
# 起動中であれば停止する
docker stop コンテナ名
docker commit コンテナ名 名付けたいイメージ名
```

#### push

- 下記の流れでdockerhubへpush
  1. dockerhubへログイン `docker login`
  1. タグ付け `docker tag 元イメージ リポジトリ名/イメージ名:タグ`
  1. push `docker push リポジトリ名/イメージ名:タグ`

### コンテナ関連

- コンテナの中でbashを利用して起動

```sh
# ※-iはコンテナの標準入力を有効化
# ※-tはttyを有効化
docker run -it イメージ名 bash
# ユーザーを指定して起動
docker run -it --user root イメージ名 bash

# 作成するコンテナに名前をつける場合
docker run --name 名付けたいコンテナ名 -it イメージ名 bash

# ポートマッピング
docker run -p 80(ローカルホストポート):80(コンテナポート) --name 名付けたいコンテナ名 -it イメージ名 bash
```

- コンテナの状態確認

```sh
# 起動コンテナのみ表示
docker ps
# 停止コンテナも含めて表示
docker ps -a
# コンテナサイズも含めて表示
docker ps -s
# 停止コンテナも含めてコンテナIDのみ表示
docker ps -aq
```

- コンテナを起動

```sh
docker start コンテナ名
```

- コンテナを停止

```sh
docker stop コンテナ名
```

- バックグランドで起動しているコンテナに入る

```sh
docker attach コンテナ名
docker exec -it コンテナ名 bash
```

- dockerコンテナを削除

```sh
docker rm コンテナ名 or コンテナID
```

- dockerコンテナの標準出力のログを確認

```sh
docker logs コンテナ名 or コンテナID
```

- ファイルコピー

```sh
docker cp {コンテナID|コンテナ名}:ファイルパス コピー先
```

```yml
# composerイメージ(公式)からファイルコピーの例
COPY --from=composer:2.7.7 /usr/bin/composer /usr/bin/composer
```

### ネットワーク関連

- dockerネットワークを表示

```sh
docker network ls
```

- dockerネットワークの詳細を確認

```sh
docker network inspect ネットワーク名 or ネットワークID
```

- dockerネットワークを作成

```sh
docker network create --attachable -d ネットワーク名 --subnet=172.17.0.0/16 作成ネットワーク名
```

### 調査

```sh
# Dockerが使用しているディスクスペースの概要を表示。dfはdisk freeの略
docker system df
docker system df -v # 詳細表示

# リアルタイムのメモリ使用量を表示
docker stats
```

### 整理

```sh
# ビルドキャッシュ削除
docker builder prune
# 未使用のイメージ削除
docker image prune
# 未使用のコンテナ削除
docker container prune
# 未使用のネットワーク削除
docker network prune
# ボリューム削除
docker volume prune
# 未使用のイメージ、コンテナ、ネットワーク、ボリュームを削除
docker system prune -a
```

## Dockerfile

- arg

```yml
FROM --platform=linux/amd64 php:8.1.29-fpm

ARG stage=inspection  # Dockerfile内でのみ有効
ENV STAGE=development # イメージ内でも環境変数として有効
```

## [マルチCPUアーキテクチャ](https://docs.docker.jp/docker-for-mac/multi-arch.html)

- [公式参考サイト:マルチCPUアーキテクチャ](https://matsuand.github.io/docs.docker.jp.onthefly/desktop/multi-arch/)
- [公式参考サイト:Docker Buildx](https://matsuand.github.io/docs.docker.jp.onthefly/buildx/working-with-buildx/)

### [docker buildx](https://matsuand.github.io/docs.docker.jp.onthefly/buildx/working-with-buildx/)

- **Docker Desktop for WindowsまたはDocker Desktop for Macの場合には、最初からbuildxが有効になっていてマルチCPUアーキテクチャーのコンテナーを作成することができるようになっている**

```sh
# 現在のビルダーインスタンスの一覧
docker buildx ls

# dockerの試験的機能を有効にしておく
DOCKER_CLI_EXPERIMENTAL=enabled

# ビルダーインスタンスを作成
docker buildx create --name ビルダー名

# ビルダーインスタンスを停止
docker buildx stop ビルダー名

# ビルダーインスタンスを削除
docker buildx rm ビルダー名

# ビルダーインスタンスを選択
docker buildx use ビルダー名

# ビルダーインスタンスを起動
docker buildx inspect --bootstrap

# マルチアーキテクチャでのビルド実行例
docker buildx build --platform linux/amd64,linux/arm64 -t イメージ名 --push .(Dockerfileのパス)
```

- Dockerfile

```yml
# 固定のプラットフォームを指定(WARN: この場合は指定したプラットフォームのみビルドされる)
FROM --platform=linux/amd64 golang:1.18

# ビルド時にプラットフォームを指定
ARG TARGETPLATFORM
FROM --platform=${TARGETPLATFORM:-linux/arm64} amazonlinux:2023.3.20240304.0
```

- docker-compose

```yml
version: '3'

services:
  db:
    image: mysql:5.7
    platform: linux/amd64
    volumes:
      - db_data:/var/lib/mysql
    restart: always```
```

## Docker for Mac

- Macでvolumeに保存されているVMに入る

```sh
screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
```

- エフェメラルポート番号 -> 49152 ~ 65535

## docker-compose

- 同期

```yml
syncs:        # ファイルやディレクトリの同期設定を行うための同期に関する設定
  syncs_name: # 任意の名前
    src: './path/to/directory'  # 対象ディレクトリを同期元として指定。ホスト側の対象ディレクトリが同期対象のディレクトリ
    sync_strategy: 'unison'     # 同期の戦略（方法）を指定。unisonは双方向のファイル同期ツールであり、ホストとコンテナ間で変更されたファイルを効率的に同期する。ホストとコンテナ間でファイルが変更された際に、自動的に両方に変更が反映
```

- シークレット

```yml
secrets:
  ssh_key:
    file: ${HOME}/.ssh/鍵の名前
```

- 依存関係設定

```yml
depends_on: # appのサービスに対して依存するため、サービス起動はappが起動してからとなる
      - app
```

- メモリ制限設定

```yml
version: "3.9"

services:
  app:
    build: .
    deploy:           # デプロイ時のリソース管理設定を指定
      resources:      # コンテナのリソース制限（メモリなど）設定
        limits:       # 最大メモリ制限（超えるとコンテナが強制終了される
          memory: 8G  # コンテナが使用可能な最大メモリ
        reservations: # 最小メモリ制限（コンテナが使用する最小メモリ）
          memory: 4G  # コンテナが使用する最小メモリ
```

- デバッグ

```yml
# 小技でコンテナを起動してそのまま停止すると`docker exec -it コンテナ名 bash`でコンテナ接続可能
entrypoint: ["sleep", "9999"]
```

### コマンド

```sh
# イメージをビルドしてコンテナを起動
docker-compose up --build

# コンテナのログをリアルタイムで表示
docker-compose logs -f コンテナ名

# composeの構成の中のappを起動(エントリーポイントも変更)してbashで接続
docker-compose run --entrypoint /bin/bash app
```