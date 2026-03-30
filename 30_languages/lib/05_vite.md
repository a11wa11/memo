# vite

## 概要

- 開発サーバー: ファイル変更を即座にブラウザに反映(HMR)
- バンドル: ビルド時にTypeScript/JSX → JS に変換・最適化
- 高速起動: webpack等の旧来ツールより開発サーバーの起動が非常に速い

## インストール

```sh
# プロジェクト作成
npm create vite@latest . -- --template react-ts # テンプレートに「React + TypeScript」を指定
```

## コマンド

```sh
# 基本コマンド 開発サーバー起動
vite # vite devと同じ
vite dev

# ビルド(本番モード)
vite build
vite build --mode development # 開発モード

# dist/の内容を本番と同じ挙動でローカルサーバー公開で確認
vite preview

# 起動時にブラウザを自動で開く
vite dev --open

# vite内部ログの詳細表示
DEBUG=vite:* npx vite dev
```

## 設定ファイル

- vite.config.jsで設定管理

```vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],             // Reactプラグイン有効化
})
```

以下、デフォルト値として規定されている

| 設定キー	 | デフォルト値 | 意味 |
|-|-|-|
|  publicDir | puclic | 静的ファイルのフォルダ名 |
| build.outDir | dist | ビルド出力先 |
|  |  |  |

- tsconfig.json: 自身はファイルを処理せず、2つの設定を束ねるルートファイルとして機能がよくあるパターン

```tsconfig.json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },  // 実行環境=ブラウザのDOM型(src/向け)の設定
    { "path": "./tsconfig.node.json" }  // 実行環境=Node.jsぬけの設定
  ]
}
```

## 開発環境

- ファイル置き場: `src`がソースコード本体置き場のデフォルト値。※npm create vite の公式テンプレートがこの構成で生成するため、事実上の標準

```sh
# 開発サーバー起動
vite     # vite devと同じ
vite dev
```

## vitest

- viteのテストランナー。viteと同時にインストールされるわけではない

### vitest設定ファイル

- vitestの実行設定を定義するファイル(vitest.config.ts)
   - vite側の設定を流用しつつ、テスト実行時だけ必要な条件を上書きする

```vitest.config.ts
import viteConfig from './vite.config'
import { fileURLToPath } from 'node:url'
import { mergeConfig, defineConfig, configDefaults } from 'vitest/config'

export default mergeConfig(
  viteConfig,
  defineConfig({
    test: {
      environment: 'jsdom',                                // ブラウザ風のDOM APIを使うテストをNode上で実行。他に node, happy-domなどあり
      exclude: [...configDefaults.exclude, 'e2e/**'],      // 既定の除外対象に加えてe2eディレクトリを除外
      root: fileURLToPath(new URL('./', import.meta.url)), // rootをこの設定ファイル基準のプロジェクトルートに設定
    },
  }),
)
```

### vitestコマンド

```sh
# 監視モードで実行(変更時に再実行)
npx vitest
# 全テストを1回実行
npx vitest run
# 絞り込みで実行
npx vitest run -t "対象名"
# カバレッジ付きで実行
npx vitest run --coverage
# UIモードで実行
npx vitest --ui
```


### テスト

- MSW
  - 通常のAPIモックはfetch関数自体を差し替える(例: vi.mock('fetch'))がMSWは違うアプローチで、ネットワーク層でリクエストをインターセプトする
  - 実際のfetchはそのまま動き、リクエストがネットワークに出る手前でMSWが横取りして偽のレスポンスを返す
  - メリット
    - テスト対象のコードを一切変更しない
    - fetch, axios など実装に依存しない
    - リクエストのURL・body・headerも検証できる

3つのライフサイクルフックの役割

| フック	 | タイミング | 内容 |
|-|-|-|
| beforeAll | 全テスト実行前に1回 | `server.listen()`でネットワークのインターセプトを開始 |
| afterEach | 各テストの後 | `server.resetHandlers()`でデフォルトハンドラーに戻す |
| afterAll | 全テスト完了後に1回 | `server.close()`でインターセプトを停止 |
|  |  |  |

- setupTest.ts

```typescript
import '@testing-library/jest-dom/vitest'
import { beforeAll, afterEach, afterAll } from 'vitest'
import { server } from './__tests__/mocks/server'

// 全テストファイルの実行前に1回だけMSWサーバーを起動
beforeAll(() => server.listen())

// 各テストの後にハンドラーをリセット(テスト内で server.use() した上書きを元に戻す)
afterEach(() => server.resetHandlers())

// 全テスト完了後にサーバーを停止
afterAll(() => server.close())
```


