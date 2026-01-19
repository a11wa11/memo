# React

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [React](#react)
  - [概要](#概要)

<!-- /code_chunk_output -->

## 概要

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