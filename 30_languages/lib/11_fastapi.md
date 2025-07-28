# FastAPI

## 概要

## コマンド

### インストール

```sh
# uvicornはHTTP・WebSocket 両対応で、非同期処理をネイティブサポート
pip install fastapi uvicorn
```

### 起動

```sh
uvicorn main:app --reload # 基本
uvicorn main:app --host 0.0.0.0 --port 8000 --reload # ホストやポート指定

```


## 構文

```python
# main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def read_root():
    return {"message": "Hello, FastAPI!"}
```