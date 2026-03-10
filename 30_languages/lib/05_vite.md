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

# ビルド結果をローカルで確認
vite preview
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


- `webpack-dev-server`の設定

```webpack.config.js
module.export = {
...
  // ローカルサーバーの設定
  devServer: {
    static: "dist",
    open: true,
  },
};
```
