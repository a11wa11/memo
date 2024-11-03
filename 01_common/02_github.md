# github

- [github](#github)
  - [使い方](#使い方)
  - [GitHub Copilot](#github-copilot)

## 使い方

- Personal access tokens
  - GitHubのリモートリポジトリにHTTP接続する際に、ユーザ名と合わせて必要となる情報

[トークンの作成手順](https://docs.github.com/ja/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)

```sh
# Personal access tokens発行後に下記のようにして使用する
$ git clone https://github.com/USERNAME/REPO.git
Username: YOUR_USERNAME
Password: YOUR_PERSONAL_ACCESS_TOKEN
```

- ブランチやコミット同士の比較
  - `https://github.com/リポジトリ/compare/stg...dev`のように`compare/ブランチ比較元...ブランチ比較先`のように打つ

## GitHub Copilot

- [code suggestions](https://docs.github.com/en/copilot/using-github-copilot/getting-code-suggestions-in-your-ide-with-github-copilot#prerequisites-2)
- [IDE で GitHub Copilot に質問する](https://docs.github.com/ja/copilot/using-github-copilot/asking-github-copilot-questions-in-your-ide)
