# vscode

## ショートカット

* `ctl + -` → 戻る

* playwrightのデバッグ設定例
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
			"ms-python.isort",
			"patricklee.vsnotes",
			"usernamehw.errorlens",
			"oderwat.indent-rainbow",
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

