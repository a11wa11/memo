# docker

* [centos7へdockerのインストール](https://qiita.com/kichise/items/f8e56c6d2d08eaf4a6a0)

## dockerのタグ一覧を取得するコマンドを生成

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

### コマンド

#### イメージ関連コマンド

* イメージ検索

```sh
docker search イメージ名
```

* イメージ取得

```sh
docker pull イメージ名
```

* DockerFileからイメージを作成する

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

* dockerイメージの作成履歴を確認

```sh
docker history イメージ名
```

* コンテナからイメージを作成

```sh
# 起動中であれば停止する
docker stop コンテナ名
docker commit コンテナ名 名付けたいイメージ名
```

* イメージを削除する

```sh
# 対象を指定する際はイメージ名、イメージ名:タグ、イメージIDのどれかで指定する
docker rmi イメージ名
docker rmi イメージ名:タグ
docker rmi イメージID
```

* イメージを一括削除

```sh
# イメージIDのみを一覧表示
docker images -q
# イメージ一括削除
docker rmi -f `docker images -q`
# 不使用イメージ一括削除
docker image prune -f
```

#### docker push

* 下記の流れでdockerhubへpush
  1. dockerhubへログイン `docker login`
  1. タグ付け `docker tag 元イメージ リポジトリ名/イメージ名:タグ`
  1. push `docker push リポジトリ名/イメージ名:タグ`

#### コンテナ関連コマンド

* コンテナの中でbashを利用して起動

```sh
docker run -it イメージ名 bash

# 作成するコンテナに名前をつける場合
docker run --name 名付けたいコンテナ名 -it イメージ名 bash
```

* コンテナの状態確認

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

* コンテナを起動

```sh
docker start コンテナ名
```

* コンテナを停止

```sh
docker stop コンテナ名
```

* バックグランドで起動しているコンテナに入る

```sh
docker attach コンテナ名
docker exec -it コンテナ名 bash
```

* dockerコンテナを削除

```sh
docker rm コンテナ名 or コンテナID
```

* dockerコンテナの標準出力のログを確認

```sh
docker logs コンテナ名 or コンテナID
```

* ファイルコピー

```sh
docker cp {コンテナID|コンテナ名}:ファイルパス コピー先
```

#### ネットワーク関連コマンド

* dockerネットワークを表示

```sh
docker network ls
```

* dockerネットワークの詳細を確認

```sh
docker network inspect ネットワーク名 or ネットワークID
```

* dockerネットワークを作成

```sh
docker network create --attachable -d ネットワーク名 --subnet=172.17.0.0/16 作成ネットワーク名
```

#### Docker for Mac

* Macでvolumeに保存されているVMに入る

```sh
screen ~/Library/Containers/com.docker.docker/Data/vms/0/tty
```

* エフェメラルポート番号 -> 49152 ~ 65535