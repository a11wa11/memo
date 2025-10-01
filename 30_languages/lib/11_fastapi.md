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

### parameter, requestBody, responses

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
      requestBody:
        description: "内容説明"
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                user: { type: integer, example: 3 }
                team: { type: string, example: "sales" }
      responses:
        "201":
          description: "Success operation"

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

#### スキーマオブジェクト種別

```yml
components:
  schemas:
    SampleCommon:
      type: string
      # 下記5項目はどの種別のスキーマでも使用可能
      description: "説明" # 説明
      default: "TEST"    # デフォルト値
      nullable: true     # nullを許容するかどうか
      example: "事例"     # サンプル
      deprecated: false  # 廃止かどうか

    SampleInt:
      type: integer
      # 下記項目はinteger型で使用可能
      multipleOf: 5           # 指定した数字の倍数かどうか
      minimum: 0              # 最小値
      exclusiveMinimum: false # 最小値含まない falseの場合 x>0 trueの場合 x>=0 
      maximum: 10             # 最大値
      exclusiveMaximum: true  # 最大値含まない falseの場合 x=<10 trueの場合 x<10
    SampleString:
      type: string
      format: email
      minLength: 10  # 最小文字数
      maxLength: 20  # 最大文字数
    SampleBool:
      type: boolean
    SampleArray:
      type: array
      items: {type: string}    # 配列内スキーマ定義
      minItems: 1              # 最小個数
      maxItems: 5　　　　　　　　 # 最大個数
      uniqueItems: true        # 配列内で重複を許すかどうか
    SampleEnum:
      type: string
      enum: ["blue", "red"]    # 選択肢
    SampleObject:
      type: object
      properties:
        place:
          type: object
          properties:
            name: { type: string }   # 中身はスキーマで定義
            place: { type: string }
          additionalProperties: True # 元の定義以外のスキーマを許可するかどうか
          required:                  # 必須項目定義
            - name
          minProperties: 1           # 最小オブジェクト数
          maxProperties: 4           # 最大オブジェクト数
        user:
          type: array
          items:
            type: object
            properties:
              score:
                type: integer
                enum: [1, 2, 3, 4, 5]
              comment:
                type: string
```
