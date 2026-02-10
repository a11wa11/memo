# github

- [github](#github)
  - [使い方](#使い方)
  - [GitHub Copilot](#github-copilot)
    - [チャット](#チャット)
      - [注意点](#注意点)

## 使い方

### アクセストークン発行

- Personal access tokens

  - GitHub のリモートリポジトリに HTTP 接続する際に、ユーザ名と合わせて必要となる情報

- [トークンの作成手順](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)
  1. github の setting
  2. Developer Settings(一番左下)
  3. Personal access tokens(classic)
  4. 既存のトークンから「Regenerate」で更新可能(だが新規で作った方がいい場合もある)
  5. 「repo」を選択
  6. その後、terraform init などで表示される password 欄に github ユーザー名を入力

```sh
# Personal access tokens発行後に下記のようにして使用する
$ git clone https://github.com/USERNAME/REPO.git
Username: YOUR_USERNAME
Password: YOUR_PERSONAL_ACCESS_TOKEN
```

- ブランチやコミット同士の比較
  - `https://github.com/リポジトリ/compare/stg...dev`のように`compare/ブランチ比較元...ブランチ比較先`のように打つ

### ブランチ設定

###### デフォルトブランチ

- Settings -> General -> Default branch を編集して設定

###### ブランチルール設定

- 新規作成
  - Settings -> Branches -> Add branch ruleset を押下して設定
- 更新
  - Settings -> Rules -> Rulesets を押下し、対象のルールセットを編集して設定

おすすめのルールセット

- Restrict deletions(対象ブランチ削除不可)
- Require a pull request before merging(PR 経由でしかマージできず、直接プッシュやコミット禁止)
- Block force pushes(フォースプッシュ禁止)

## GitHub Copilot

- [code suggestions](https://docs.github.com/en/copilot/using-github-copilot/getting-code-suggestions-in-your-ide-with-github-copilot#prerequisites-2)

### チャット

- [IDE で GitHub Copilot に質問する](https://docs.github.com/ja/copilot/using-github-copilot/asking-github-copilot-questions-in-your-ide)

- `Control`+`Command`+`i`
  - チャット ビューを開く
- `#editor`
  - 現在のファイルを参照する ※公式推奨の方法
- スラッシュ系
  - `/explain`
    - 対象を説明する
  - `/fix`
    - 選んだコードの問題の修正プログラムを提案します
  - `clear`
    - 会話を終了する
- `@workspace`
  - ワークスペース内のコードに関する内容。プロジェクトの構造、コードのさまざまな部分の相互作用、またはプロジェクト内の設計パターンを考慮する場合など
- `@vscode`
  - Visual Studio Code コマンドと機能に関する内容。 Visual Studio Code に関するヘルプが必要な場合など
- `@terminal`: %} Visual Studio Code ターミナル シェルとその内容に関する内容。ターミナルコマンドの作成またはデバッグに関するヘルプが必要な場合など

#### 注意点

- ファイルを開きすぎると、Copilot が正確なコードを提案するのに必要な情報を見失う可能性があるため、開いているファイルを最小限に抑えること
