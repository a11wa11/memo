# Claude

- [Claude](#claude)
  - [claude code](#claude-code)

## claude code

### CLAUDE.md

- claude code実行時に守って欲しいルールなどを記載
  - 例 ある言語のベストプラクティスを遵守など
- ディレクトリ毎に設置でき、親ディレクトリのCLAUDE.mdも読み込まれる
- 要件定義などをCLAUDE.mdに記載しておくのも1つの手段

### コマンド

```sh
claude -r(resume)               # 履歴を遡ることができる
claude --continue               # 前回途中から再開できる
claude --mcp-config=.mcp.json   # mcpサーバーと連携する
```

- `/init` CLAUDE.mdを生成する
- `/hooks` hooksを生成する
  - AIがツール(ファイルの編集など)を実行する前後や、一連の作業が完了したタイミングで、特定の処理を自動的に発火(実行)させる機能のこと
  - pre-tool-use: AIがツールを使用する直前に処理を実行
  - post-tool-use: AIがツールを使用した直後に処理を実行
- `/mcp` mcpへ接続する
- `/agents` 専用のAIを起動する(サブエージェント機能)
   - コードレビュー専用のエージェントを作って独立したコンテキストで実行される
   - セキュリティチェック専用のエージェントなども作成可能
- `commands` 自作コマンドを定義する
  - .claude/commands/ディレクトリにmdファイルを配置して定義可能

## MCPサーバー

slackやgithubなどAIと通して指示ができるサーバーのこと

- `.mcp.json`をプロジェクトルートディレクトリに配置することで、MCPサーバーとして認識される
  - mcpサーバーとして起動したい内容は各サービスで紹介されていることが多い
  - MCPify: MCPサービスを自作するサービス。なければ自作を検討
- `https://mcp.so/`で流行のMCPサーバーを検索可能
