# docker

- [docker](#docker)
  - [インストール](#インストール)
  - [dockerのタグ一覧を取得するコマンドを生成](#dockerのタグ一覧を取得するコマンドを生成)
  - [コマンド](#コマンド)
    - [イメージ関連](#イメージ関連)
      - [よく使うオプション](#よく使うオプション)
      - [commit](#commit)
      - [push](#push)
    - [コンテナ関連](#コンテナ関連)
    - [ネットワーク関連](#ネットワーク関連)
  - [Dockerfile](#dockerfile)
  - [マルチCPUアーキテクチャ](#マルチcpuアーキテクチャ)
    - [docker buildx](#docker-buildx)
  - [Docker for Mac](#docker-for-mac)
  - [docker-compose](#docker-compose)

## インストール

- [centos7へdockerのインストール](https://qiita.com/kichise/items/f8e56c6d2d08eaf4a6a0)
- mac
  - `brew install docker`

## dockerのタグ一覧を取得するコマンドを生成

<details>
    <summary>展開</summary>

```sh
# .bashrcなどに追記する

# docker-tags centos でタグ一覧を取得。jq & curl が必要

if [ -e /usr/bin/docker ]; then
    function docker-tags {
        curl -s https://registry.hub.docker.com/v2/repositories/library/$1/tags/ | jq -r '.results[].name'
    }
fi
```

```sh
# Google chrome & driver インストール
RUN yum install -y https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm --skip-broken
```

</details>

## コマンド

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

# マルチステージビルドで定義されたイメージを指定してbuild
docker build --target 定義されたイメージ -t 名付けたいイメージ名 .(Dockerfileのあるパス)

# マルチステージビルドで定義されたイメージを指定してbuild
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

**Docker Desktop for WindowsまたはDocker Desktop for Macの場合には、最初からbuildxが有効になっていてマルチCPUアーキテクチャーのコンテナーを作成することができるようになっている**

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
FROM --platform=linux/amd64 golang:1.18
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

- 依存関係設定

```yml
depends_on: # appのサービスに対して依存するため、サービス起動はappが起動してからとなる
      - app
```
