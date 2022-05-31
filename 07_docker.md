# docker

* [centos7へdockerのインストール](https://qiita.com/kichise/items/f8e56c6d2d08eaf4a6a0)

## dockerのタグ一覧を取得するコマンドを生成

```sh
# .bashrcなどに追記する

# docker-tags centos でタグ一覧を取得。jq & curl が必要
if [ -e /usr/bin/docker ]; then
    function docker-tags {
        curl -s https://registry.hub.docker.com/v1/$1/tags | jq -r '.[].name'
    }
fi
```

```sh
# Google chrome & driver インストール
RUN yum install -y https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm --skip-broken
```

### docker build

```sh
# 基本build
docker build -t 名付けたいイメージ名 .(Dockerfileのあるパス)
# Dockerfileを指定してbuild
docker build -f Dockerfile_demo(Dockerfile以外の名前の場合) xxx/yyy/path(パス)
```

### dockerの小技

* イメージを一括削除

```sh
# イメージIDのみを一覧表示
docker images -q
# イメージ一括削除
docker rmi -f `docker images -q`
```

* エフェメラルポート番号 -> 49152 ~ 65535
