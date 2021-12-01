# cypress

## 注意点

1. 別タブを開いた動作確認が出来ない
1. SafariやIEでは使用不可
1. クロスドメインアクセスが不可能(google.com→apple.comなどへ移行してテストすることは不可)

## インストール

### cypressはnode.jsのライブラリであるため、以下の流れでインストール

1. node.jsのインストールする
1. node.jsのパッケージ管理マネージャーであるnpmを使用してcypressをインストール

#### 1. node.jsのインストール。とりあえず、簡単かつ代表的なインストール方法は下記２つ

* [webサイト](https://nodejs.org/en/download/)からインストール
* [nodenv](https://github.com/nodenv/nodenv)からのインストール
  * ```nodenv install バージョン(17.0.1)```
  * ```echo 'eval "$(nodenv init -)"' >> ~/.zshrc # 初回のみ```

#### 2. npmからcypressのインストール

```sh
npm install cypress            #グローバルインストール
npm install cypress --save-dev #ローカルインストール
npm uninstall --no-fund cypress --save-dev

# インストール完了後、確認
$ npx cypress --version

Cypress package version: 8.7.0
Cypress binary version: 8.7.0
Electron version: 14.1.0
Bundled Node version: 14.17.0
```

### npm

```sh
# 設定確認
npm config list

# パッケージ確認
npm list

# パッケージ情報確認
npm info パッケージ名

# パッケージアップデート
npm update パッケージ名

```

* cypressの実行

```sh
# cypressウィンドウを開いて実行
npx cypress open
./node_modules/.bin/cypress open

# cliで実行
npx cypress run
npx cypress run  --headed
npx cypress run  --browser chrome

```

* テスト内で主な挙動

```javascript
# 指定のURLへ移動する
cy.visit("https://www.google.com/")

# クリックする
cy.get('[href="https://abcde.com]'').click()

# チェックボックスにチェックを入れる
cy.get('#agree').check({force: true}) 
cy.get('[type="radio"]').first().check() #ラジオボタンの１つ目にチェックを入れる 

# この行からステップ実行が可能
cy.pause()

# スクリーンショットを撮る
cy.screenshot('test01')
```

* 要素の取得
  * 基本的にcypressのtestrunnerで要素が取得可能
  * testrunnerのselectorが常に正しいとは限らない

```javascript
# 親に.productsを持つ`.product`子オブジェクトを取得する
cy.get('.products').find('.product')

# 親に.productsを持つ`.product`子オブジェクトの中から`ADD TO CART`の内容を含む2の値を持つオブジェクトを取得する
cy.get('.products').find('.product').eq(2).contains('ADD TO CART')

# 親オブジェクトを取得する
cy.get('.child').parent()

# タイトルを取得する
cy.title()
cy.get('title')
```

* 入力系

```javascript
＃ 文字を入力
cy.get('textarea').type('Hello, World')

# 文字を消去
cy.get('textarea').clear()
```

* 検証

```javascript
// assertion
cy.get('#main_title').should('have.text', 'Content title') # 完全一致
cy.title().should('eq', 'sample page title')               # 完全一致
cy.title().should('inclue', 'requried')                    # 一部一致
should('have.attr', 'href').and('include', 'sample_text')  # 複数条件
cy.get('ul').children().should('have.length', 3)           # 要素数一致
cy.get('.child').parent().should('have.class', 'parent')   # 親クラス一致
```

[参考](https://future-architect.github.io/articles/20210428a/)
[公式](https://docs.cypress.io/guides/getting-started/installing-cypress#Opt-out-of-sending-exception-data-to-Cypress)

https://engineer-ninaritai.com/cypress-install/  
https://www.kaizenprogrammer.com/entry/2017/12/23/144242  
https://yaruki-strong-zero.hatenablog.jp/entry/cypress  
https://qiita.com/eyuta/items/a2454719c2d82c8bacd5  
