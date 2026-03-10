# React

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [React](#react)
  - [概要](#概要)

<!-- /code_chunk_output -->

## 用語

| 用語	 | 意味 | 補足 |
|-|-|-|
| jsx | JavaScriptの中にHTMLっぽいタグを書ける記法 | jsxでは大文字始まりのタグはすべてコンポーネント(関数)の呼び出し |
| コンポーネント | UIの部品(パーツ) | 実態はreactの関数 |
| レコード2 | 3 | 4 |
|  |  |  |
|  |  |  |
|  |  |  |

### jsx

- 中身(子要素)がないタグは、開始と終了をまとめて書ける

```jsx
// この2つは同じ意味
<PublishedPage></PublishedPage>
<PublishedPage />   // ← 省略形(自己閉じタグ)
```

## コード例

```javascript
import {StrictMode} from 'react';            // Reactの厳格モード
import {createRoot} from 'react-dom/client'; // ReactのDOMレンダリング

const App = () => {
  return ( // 複数行の場合は()で囲む
    // <React.Fragment>と<>と同義
    <div>
      <h1>Hello World</h1>
    </div>
  );
};

const root = createRoot(document.getElementById('root')); // DOMレンダリング
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);

// 応用1
import React {StrictMode} from 'react';            // Reactの厳格モード
import {createRoot} from 'react-dom/client'; // ReactのDOMレンダリング

const App = () => {
  return (
    <React.Fragment>
      <h1>Hello World</h1>
    </React.Fragment>
  );
};

const root = createRoot(document.getElementById('root')); // DOMレンダリング
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

### react-router

BrowserRouter, Routes, Route, Navigate はHTMLタグではなく、react-routerライブラリが提供するコンポーネント(= JSXで使えるタグ)

| コンポーネント | 役割 | 説明 |
|---|---|---|
| `BrowserRouter` | ルーティングの有効化 | アプリ全体を包み、URLの変更を監知してページ遷移(画面の切り替え)を可能にする。通常`main.tsx`で1回だけ使う |
| `Routes` | ルート定義のグループ化 | 複数の`Route`をまとめ、現在のURLに一致する`Route`を1つ選んで表示する |
| `Route` | URLとコンポーネントの対応付け | `path`で指定したURLにアクセスされたとき、`element`で指定したコンポーネントを表示する |
| `Navigate` | リダイレクト(自動転送) | 表示された瞬間に`to`で指定したURLへ自動的に移動する。画面には何も表示しない |

```jsx
function App() {
  return (
    <Routes>
      <Route element={<MainLayout />}>
        <Route index element={<Navigate to="/test1" replace />} /> //
        <Route path="test1" element={<TestPage1 />} />
        <Route path="test2" element={<TestPage2 />} />
      </Route>
    </Routes>
  );
}

```

```text
# replaceなし(デフォルト)
履歴: ... → / → /published-data
「戻る」ボタン → / に戻る → またリダイレクト → 無限ループ的な動き

# replaceあり ← こっちを使用
履歴: ... → /published-data  (/ は履歴に残らない)
「戻る」ボタン → 前のサイトに戻る(正常)
```

## 各ファイル

- main.tsx
  - ブラウザの`<div id="root">`にReactを起動
- App.tsx
  - 最初にマウントされるのがApp.tsx (ルートコンポーネント)
