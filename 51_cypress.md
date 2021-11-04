# cypress

## インストール

### cypressはnode.jsのライブラリであるため、以下の流れでインストール

1. node.jsのインストールする
1. node.jsのパッケージ管理マネージャーであるnpmを使用してcypressをインストール

#### 1. node.jsのインストール。とりあえず、簡単かつ代表的なインストール方法は下記２つ

* nodenvからのインストール → ```nodenv install バージョン```
  * ```echo 'eval "$(nodenv init -)"' >> ~/.zshrc # 初回のみ```
* [webサイト](https://nodejs.org/en/download/)からインストール

#### 2. npmからcypressのインストール

```sh
npm install cypress

# インストール完了後、確認
$ npx cypress --version                                                                                                                               [/Users/awa_m1/project01/test-automation : 21-10-28 23:03:34]
Cypress package version: 8.7.0
Cypress binary version: 8.7.0
Electron version: 14.1.0
Bundled Node version: 14.17.0
```

* cypressの実行

```sh
npx cypress open
```

[参考](https://future-architect.github.io/articles/20210428a/)
[公式](https://docs.cypress.io/guides/getting-started/installing-cypress#Opt-out-of-sending-exception-data-to-Cypress)


https://engineer-ninaritai.com/cypress-install/
https://www.kaizenprogrammer.com/entry/2017/12/23/144242
https://yaruki-strong-zero.hatenablog.jp/entry/cypress
https://qiita.com/eyuta/items/a2454719c2d82c8bacd5
