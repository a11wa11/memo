# vscode

## ショートカット

* `ctl + -` → 戻る

* playwrightのデバッグ設定例
  * launch.json

```json
"configurations": [
    // {
    //     "type": "pwa-node",
    //     "request": "launch",
    //     "name": "Run Test - headed",
    //     "args":["test", "--headed", "${relativeFile}"]
    // }

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
