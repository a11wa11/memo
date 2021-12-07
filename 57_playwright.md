# playwright

## インストール

```sh
npm install --save-dev playwright
npm i -D playwright
```

## [基本](https://playwright.dev/docs/api/class-page)

* [入力](https://playwright.dev/docs/api/class-locator#locator-fill)

```typescript
page.locator.fill(value[, options])      //一瞬で入力する
page.fill(selector, value[, options])    // 〃

page.locator.type(text, {delay: 100})​    // タイピングして入力する
page.type(selector, text, {delay: 100})​  // 〃
```

* [存在確認](https://playwright.dev/docs/api/class-page#page-query-selector)

```typescript
page.$(selector[, options])         //最初の１個のみマッチ
page.$$(selector[, options])        //対象は全てマッチ
page.isVisible(selector[, options])​ //画面で閲覧可能ならばtrue

if (await page.$(selector[, options])) {.....}
```

* [タブ移動](https://playwright.dev/docs/api/class-page#page-bring-to-front)

```typescript
page.bringToFront()
```

* [指定時間待つ](https://playwright.dev/docs/api/class-page#page-wait-for-timeout)

```typescript
page.waitForTimeout(1000) //1000=1秒
```

## デバッグ

* コマンド実行

```sh
PWDEBUG=1 npx playwright test 対象テスト --headed
PWDEBUG=console npx playwright test 対象テスト --headed
npx playwright test 対象テスト --headed --debug
```

```typescript
      if ( page1.$('text=gmail.com') ) {
        await page1.click('text=gmail.com')
        test.setTimeout(0)
        await page.pause()
        await page1.click('text=Delete')
      }
```

* [vscodeでのデバッグ設定](https://qiita.com/sakamoto66/items/d7a0977601ec94b88e98)

```json
    "version": "0.2.0",
    "configurations": [
        {
            "type": "pwa-node",
            "request": "launch",
            "name": "Run Test - headed",
            # playwrightを実行するパスを指定
            "program": "node_modules/.bin/playwright",
            # 実行時に指定したい引数
            "args":["test", "--headed", "${relativeFile}"]
        }
    ]
```

## [アサーション](https://playwright.dev/docs/test-assertions)

## [環境設定](https://playwright.dev/docs/api/class-testoptions)

* 多言語のwait文のように待ち時間をつくる

```typescript
test.setTimeout(0) //デフォルトでは各テストの実施時間は30秒だが、無限にする
```

* デフォルト環境設定
  * [参考](https://playwright.dev/docs/api/class-testoptions)

`playwright.config.ts`というファイルをプロジェクトルートフォルダに配置すると他のテストファイルに自動反映させることが可能

* [projectsに関する参考](https://github.com/microsoft/playwright/issues/2973#issuecomment-971608969)
* [ブラウザに関する参考](https://webrtc.org/getting-started/testing)
* [Configuration](https://playwright.dev/docs/test-configuration)
* [TestOptions](https://playwright.dev/docs/api/class-testoptions#test-options-launch-options)
* [TestProject](https://playwright.dev/docs/api/class-testproject)

```typescript
import { PlaywrightTestConfig, devices } from '@playwright/test';
const config: PlaywrightTestConfig = {
  use: {
    browserName: 'chromium',                     // ブラウザの指定
    // browserName: 'firefox',
    // browserName: 'webkit',
    locale: 'en-US',                             // 言語指定
    headless: false, // Test in browser view     // ブラウザなし（コマンドのみ）かどうか
    viewport: { width: 1920, height: 720 },      // ブラウザのサイズ指定
    ignoreHTTPSErrors: true,
    // baseURL: 'https://playwright.dev/',       // 各テストの初期URL
    permissions: ['microphone', 'camera'],       // マイクとカメラの設定
    launchOptions: { args: ['--use-fake-device-for-media-stream', '--use-fake-ui-for-media-stream'] },
    screenshot: 'on',                            // スクリーンショットの設定
    video: 'on'                                  // ビデオの設定
  },
  retries: 1,                                    // テストで失敗した時のリトライ回数。デフォルトでは0なのでリトライなし
  // workers: 2,
  timeout: 60000,                                // 各テストのタイムアウト時間設定。1000=1秒。60000=1分
  projects: [ // Run in 3 browsers: chrome, Fire Fox, Safari
    {
      name:"chrome",
      use:{
        browserName:"chromium",
        permissions:["camera", 'microphone'],
        launchOptions:{ args:[
            "--no-sandbox",
            "--disable-setuid-sandbox",
            "--use-fake-device-for-media-stream",
            "--use-fake-ui-for-media-stream",
            "--use-file-for-fake-video-capture=./data/fakeCameraCapureDP.y4m"
        ]}
      }
    },
    {
      name:"firefox",
      use:{
        browserName:"firefox",
        launchOptions:{
          args:[
            "--quiet",
            "--use-test-media-devices" 
          ],
          firefoxUserPrefs: { 
            "media.navigator.streams.fake": true,
            "media.navigator.permission.disabled": true 
          }
        }             
      },
    },
    {
      name:"safari",
      use:{
        browserName:"webkit",
        launchOptions:{ args:[
          "--enable-mock-capture-devices=true",
          "--enable-media-stream=true"
        ]}
      }
    }
  ]
};
export default config;
```

* セレクタ

```sh
adminPage.waitForSelector('li div span'); # 指定したセレクターを取得するまで待つ
```
