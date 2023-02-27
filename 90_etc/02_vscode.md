# vscode

## ショートカット

* `cmd + P` → コマンドパレット
* `ctl + -` → 戻る
* `cmd + K + T` → カラーテーマ設定

## playwrightのデバッグ設定例
  
* launch.json

```json
"configurations": [
    {
        "type": "pwa-node",
        "request": "launch",
        "name": "playwright debug",
        "env": { "PWDEBUG": "console" },
        "program": "node_modules/.bin/playwright",
        "args":["test", "${relativeFile}"]
    },
    {
        "type": "pwa-node",
        "request": "launch",
        "name": "Run Test - headless",
        "program": "node_modules/.bin/playwright",
        "args":["test", "${relativeFile}"]
    },
    {
        "type": "pwa-node",
        "request": "launch",
        "name": "Run Test - headed",
        "program": "node_modules/.bin/playwright",
        "args":["test", "--headed", "${relativeFile}"]
    }
]
```

## 使用したい拡張機能
  
* extensions.json

```json
{
 // List of extensions which should be recommended for users of this workspace.
 "recommendations": [
   //  common
   "streetsidesoftware.code-spell-checker",
   "ms-azuretools.vscode-docker",
   "ms-vscode-remote.remote-containers",
   "donjayamanne.githistory",
   "eamodio.gitlens",
   "tyriar.sort-lines",
   "patricklee.vsnotes",
   "yzhang.markdown-all-in-one",
   "usernamehw.errorlens",
   "oderwat.indent-rainbow",
   "moshfeu.compare-folders",
   "vscode-icons",
   "hediet.vscode-drawio",
   "Log File Highlighte",
   //  Infrastructure
   "aws-scripting-guy.cform",
   "redhat.vscode-yaml",
   //  languages
   "ms-python.python",
   "ms-python.vscode-pylance",
   "tabnine.tabnine-vscode",
 ],
 // List of extensions recommended by VS Code that should not be recommended for users of this workspace.
 "unwantedRecommendations": [
 ]
}
```

### markdown

* コマンドパレットで`Markdown All in One: Create Table of Contents`を実行すると、見出しの内容から目次が作成される
* 見出し更新後に`Cmd+s`で保存すると、目次も更新される
