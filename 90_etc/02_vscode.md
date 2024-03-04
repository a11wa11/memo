# vscode

- [vscode](#vscode)
  - [ショートカット](#ショートカット)
    - [よく使う機能](#よく使う機能)
  - [playwrightのデバッグ設定例](#playwrightのデバッグ設定例)
  - [使用したい拡張機能](#使用したい拡張機能)
    - [markdown](#markdown)

## ショートカット

- 特によく使う
  - `cmd + P` → コマンドパレット
  - `ctl + -` → 戻る
  - `cmd + K + T` → カラーテーマ設定

<table>
    <tr>
      <th>操作概要</th>
      <th>Windows</th>
      <th>macOS</th>
    </tr>
    <tr>
      <td>全般</td>
    </tr>
    <tr>
      <td>すべてのコマンドの表示</td>
      <td>Ctrl+Shift+P, F1</td>
      <td>⇧⌘P, F1</td>
    </tr>
    <tr>
      <td>クイックオープン</td>
      <td>Ctrl+P</td>
      <td>⌘P</td>
    </tr>
    <tr>
      <td>キーボード ショートカットを開く</td>
      <td>Ctrl+K Ctrl+S</td>
      <td>⌘K ⌘S</td>
    </tr>
    <tr>
      <td>
        直近で開いたファイルを表示
      </td>
      <td>Ctrl+Tab</td>
      <td>⌃Tab</td>
    </tr>
    <tr>
      <td>編集</td>
    </tr>
    <tr>
      <td>コードの整形</td>
      <td>Shift+Alt+F</td>
      <td>⇧⌥F</td>
    </tr>
    <tr>
      <td>（選択範囲なしで）行の切り取り</td>
      <td>Ctrl+X</td>
      <td>⌘X</td>
    </tr>
    <tr>
      <td>（選択範囲なしで）行のコピー</td>
      <td>Ctrl+C</td>
      <td>⌘C</td>
    </tr>
    <tr>
      <td>行を下へ移動</td>
      <td>Alt+↓</td>
      <td>⌥↓</td>
    </tr>
    <tr>
      <td>行を上へ移動</td>
      <td>Alt+↑</td>
      <td>⌥↑</td>
    </tr>
    <tr>
      <td>行を下へコピー</td>
      <td>Shift+Alt+↓</td>
      <td>⇧⌥↓</td>
    </tr>
    <tr>
      <td>行を上へコピー</td>
      <td>Shift+Alt+↑</td>
      <td>⇧⌥↑</td>
    </tr>
    <tr>
      <td>行の削除</td>
      <td>Ctrl+Shift+K</td>
      <td>⇧⌘K</td>
    </tr>
    <tr>
      <td>行を下に挿入</td>
      <td>Ctrl+Enter</td>
      <td>⌘Enter</td>
    </tr>
    <tr>
      <td>行のインデント追加</td>
      <td>Ctrl+]</td>
      <td>⌘]</td>
    </tr>
    <tr>
      <td>行のインデント削除</td>
      <td>Ctrl+[</td>
      <td>⌘[</td>
    </tr>
    <tr>
      <td>名前を変更</td>
      <td>F2</td>
      <td>F2</td>
    </tr>
    <tr>
      <td>補完候補を開く</td>
      <td>Ctrl+Space</td>
      <td>⌃Space</td>
    </tr>
    <tr>
      <td>選択</td>
    </tr>
    <tr>
      <td>矩形選択(マウス選択)</td>
      <td>Shift+Alt+ドラッグ</td>
      <td>⇧⌥+ドラッグ</td>
    </tr>
    <tr>
      <td>矩形選択(キーボード選択)</td>
      <td>Ctrl+Shift+Alt+方向キー</td>
      <td>⇧⌥⌘+方向キー</td>
    </tr>
    <tr>
      <td>選択範囲と同じ文字列の複数指定</td>
      <td>Ctrl+D</td>
      <td>⌘D</td>
    </tr>
    <tr>
      <td>移動</td>
    </tr>
    <tr>
      <td>行の先頭に移動</td>
      <td>Home</td>
      <td>Home</td>
    </tr>
    <tr>
      <td>行の最後に移動</td>
      <td>End</td>
      <td>End</td>
    </tr>
    <tr>
      <td>ファイルの先頭に移動</td>
      <td>Ctrl+Home</td>
      <td>⌘↑</td>
    </tr>
    <tr>
      <td>ファイルの末尾に移動</td>
      <td>Ctrl+End</td>
      <td>⌘↓</td>
    </tr>
    <tr>
      <td>行コメントの切り替え</td>
      <td>Ctrl+/</td>
      <td>⌘/</td>
    </tr>
    <tr>
      <td>指定行へ移動</td>
      <td>Ctrl+G</td>
      <td>⌃G</td>
    </tr>
    <tr>
      <td>ファイルに移動</td>
      <td>Ctrl+P</td>
      <td>⌘P</td>
    </tr>
    <tr>
      <td>前に戻る</td>
      <td>Alt+←</td>
      <td>⌃-</td>
    </tr>
    <tr>
      <td>次に進む</td>
      <td>Alt+→</td>
      <td>⌃⇧-</td>
    </tr>
    <tr>
      <td>検索</td>
    </tr>
    <tr>
      <td>ファイル内検索</td>
      <td>Ctrl+F</td>
      <td>⌘F</td>
    </tr>
    <tr>
      <td>ファイル内置換</td>
      <td>Ctrl+H</td>
      <td>⌥⌘F</td>
    </tr>
    <tr>
      <td>次を検索</td>
      <td>F3</td>
      <td>⌘G</td>
    </tr>
    <tr>
      <td>前を検索</td>
      <td>Shift+F3</td>
      <td>⇧⌘G</td>
    </tr>
    <tr>
      <td>プロジェクト内検索</td>
      <td>Ctrl+Shift+F</td>
      <td>⇧⌘F</td>
    </tr>
    <tr>
      <td>パネル操作</td>
    </tr>
    <tr>
      <td>サイドバーの表示/非表示</td>
      <td>Ctrl+B</td>
      <td>⌘B</td>
    </tr>
    <tr>
      <td>ターミナルの表示/非表示</td>
      <td>Ctrl+@</td>
      <td>⌃@</td>
    </tr>
    <tr>
      <td>新しく統合ターミナルを表示</td>
      <td>Ctrl+Shift+@</td>
      <td>⌃⇧@</td>
    </tr>
    <tr>
      <td>エクスプローラーの表示</td>
      <td>Ctrl+Shift+E</td>
      <td>⇧⌘E</td>
    </tr>
</table>

### よく使う機能

- ファイル検索
  - `cmd + P`で任意のファイル名(READMEなど)を入力

## playwrightのデバッグ設定例
  
- launch.json

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
  
- extensions.json

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

- コマンドパレットで`Markdown All in One: Create Table of Contents`を実行すると、見出しの内容から目次が作成される
- 見出し更新後に`Cmd+s`で保存すると、目次も更新される
