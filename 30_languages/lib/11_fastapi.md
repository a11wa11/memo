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

```python
# リクエストボディの定義時などの例
from pydantic import BaseModel
class RequestName(BaseModel):
    user_id: str
    class Config:
        extra = "allow" # 定義外の値をそのまま許可して取込む
```

## OpenAPI

- ルートオブジェクト

```yml
# 必須
openapi: "3.0.3" # OpenAPIバージョン

# 必須
info:            # メタデータ
  title: Sample
  version: "1.0.0"

servers: []      # APIを提供するサーバー

tags: []         # 分類してタグ付けするため

# 必須
paths: {}        # 利用可能なパスを定義

security: []     # API全体にかけるセキュリティ要件

components: {}   # 部品化して共通利用する
```

- 共通項目

```yml
openapi: "3.0.3"

info:
  title: Sample
  # マークダウン記述可能
  description: |
  # 概要
  - ユーザー
  - プロダクト
  version: "1.0.0"


paths: {}
```

### parameter

```yml
openapi: "3.0.3"

info:
  title: Sample
  # マークダウン記述可能
  description: |
  # 概要
  - ユーザー
  - プロダクト
  version: "1.0.0"


paths: {}
    get:
      parameters:
      # パスパラメーター
      - PathUserId:
        description: ユーザーID
        name: user_id
        in: path
        required: true # パスの場合は必ずtrueになる
        schema: { type: string }
      # クエリパラメーター
      - Limit:
        description: 1回あたりの取得件数
        name: limit
        in: query
        required: false
        schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
```

### components

```yml
components:    # 共通部品置き場
  schemas:     # データ構造（リクエスト/レスポンス）
    StartTime:
      type: string
      description: 始業時刻[形式：hh:mm]
      example: "09:00" # exampleは例1つ
    EndTime:
      type: string
      description: 終業時刻[形式：hh:mm]
      examples: # examplesは例複数
        morning:
            summary: "午前"
            value: "09:00"
        evening:
            summary: "午後"
            value: "18:30"
    UserId:
      type: string
      description: ユーザーID
      required: true

  parameters:  # クエリ/パスパラメータ
    UserIdParam:
      name: id
      in: query
      required: true
      schema:
        $ref: '#/components/schemas/UserId'
    UserNameParam:
      name: name
      in: query
      required: true
      schema:
        $ref: '#/components/schemas/UserName'
  headers:     # ヘッダー定義
```
