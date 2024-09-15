# json

## jsonからymlへ変換するツール

### yq

- インストール

```sh
sudo apt install yq      # Ubuntu/Debianの場合
brew install yq          # macOSの場合
```

- コマンド

```sh
# yml形式で出力
cat ファイル名.json | yq -p json

# ノードを出力
yq '.Resources.ServerlessDeploymentBucketPolicy' < ファイル名.yml
```

### json2yaml

- インストール

```sh
npm install -g json2yaml
```

- コマンド

```sh
npx json2yaml -d レベル番号 ファイル名.json > ファイル名.yml
```
