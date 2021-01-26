# docker

* centos7へdockerのインストール
https://qiita.com/kichise/items/f8e56c6d2d08eaf4a6a0

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
