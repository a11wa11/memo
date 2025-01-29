# json

## 整形

### vscode

1. ’シングルクォート'ではなく"ダブルクオート"で囲まれていること
1. 右下のステータスバーのファイルタイプをJSONファイルに変更
1. VSCodeでJSONファイルを開いた状態で、`Shift + Alt + F` (Windows) または `Shift + Option + F` (Mac) のショートカットキー、または右クリックを使って「ドキュメントのフォーマット」を実行

### jq

```sh
# 特定の値を取得する
cat ***.json | jq ".キー名"
# row形式で表示(ダブルクオテーションの除去など)
cat ***.json | jq -r
# resultという任意のキーを作成してその内容を決定
cat ***.json | jq -r '.StackSummaries[] | .任意のキー = .StackStatus + " " + .LastUpdatedTime + " " + .StackName | .任意のキー'
```

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
