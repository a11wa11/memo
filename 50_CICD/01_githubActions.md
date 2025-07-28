# CI/CD

- [CI/CD](#cicd)
  - [github actions](#github-actions)
    - [デバッグ](#デバッグ)

CI/CD(Continuous Integration/Continuous Deployment)はアプリケーション開発にて効率的にビルド、テスト、デプロイするための方法

## github actions

```yml
name: ワークフロー名

on:                                # ワークフローのトリガー条件
  workflow_dispatch:               # 手動で実施できる
  push:                            # ブランチに新しいコミットがプッシュされた場合
    branches:                      # 対象のブランチを指定
      - dev
    paths:                         # 対象ディレクトリ以下の変更がある場合に限る
      - "XXX/app/**"

  schedule:                        # 指定日程で実施
   - cron: '0 18 * * *'

jobs:                           　 # ワークフロー内のジョブを定義
  ジョブ名:                         # ジョブ名を定義
    name: Run end-to-end tests
    runs-on: ubuntu-latest         # ジョブが実行される仮想マシンの環境を指定
    # runs-on: ubuntu-20.04
    # runs-on: windows-2019
    # runs-on: macos-latest
    env:                           # 環境変数を定義
        NODE_ENV: 16.19.0

    steps:                         # ジョブ内のステップを定義。各ステップは特定のアクションを実行
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_ENV }}
      - name: Install dependencies
        run: npm ci
      - name: Install playwright browsers
        run: npx playwright install-deps chromium # install dependencies for Chromium only
      - name: Run tests
        run: npx playwright test --config playwright.config.ts 'spec.ts' --project=chrome
```

- 指定のファイルを保存する

```yml
      - name: Upload output file
        if: always()             # 先行のステップが失敗しても常時実行する
        uses: actions/upload-artifact@v3
        with:
          name: output-log-file
          path: output.log
```

### デバッグ

- `act`はGitHub ActionsのワークフローをローカルDocker環境で実行・デバッグできるCLIツール

```sh
# インストール
brew install act

# 確認
act -l       # workflowsリストを確認
act --dryrun # 実際に実行せずテスト

# 実行
## デフォルトイベント（pull_requestなど）で実行
act
act -v 　　　　　　　　　　　　　　　　　　　　     # 冗長出力
act workflow_dispatch(push, pull_requestなど) # イベントを指定する
act --container-architecture linux/arm64     # アーキテクチャを指定

## リポジトリ直下で全ワークフローをpushイベント相当で実行
act push
## 特定ジョブだけ実行
act -j build

# シークレット使用
## シークレットを直接指定
act -s MY_SECRET=foo
## .secretsファイルを利用
echo "MY_SECRET=foo" > .secrets
act --secret-file .secrets(シークレットファイル名)
```
