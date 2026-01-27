# API

## リクエストヘッダー


### Content-Type

サーバーがデータを正しく解釈するために必要な項目

```text
# パスカルケース(Content-Type)が慣例
Content-Type: application/json; charset=utf-8
```

- GET: リクエスト時は不要
- POST/PUT: サーバがボディの解釈方法を知るために必須
  - POSTでbodyなしなら送るデータがないので不要の場合あり


| Content-Type | 用途 |
|--------------|------|
| `application/json` | JSON（REST API で最も一般的） |
| `application/x-www-form-urlencoded` | HTMLフォーム送信（キー=値&キー=値） |
| `multipart/form-data` | ファイルアップロード |
| `text/html` | HTML |
| `text/plain` | プレーンテキスト |
| `text/csv` | CSV |
| `application/xml` | XML |
| `application/pdf` | PDF |
| `image/png`, `image/jpeg` | 画像 |
| `application/octet-stream` | バイナリ（汎用） |

### charset

| charset | 説明 |
|---------|------|
| `utf-8` | 世界標準（絵文字も対応） |
| `shift_jis` | 日本の古いシステム |
| `euc-jp` | 日本のUnix系 |
| `iso-8859-1` | 西欧言語 |

### X-Request-ID

- リクエストを一意に識別するためのID
  - 形式（UUID v4 例`A0E7CA6D-E568-4FED-8562-318E0620F47F`(8-4-4-4-12 の形式)が一般的）
- `X-`を付けないことが推奨されているが、X-Request-IDは広く使われているため定着している


### Authorization

- 認証情報を送るための標準ヘッダー
  - 「あなたは誰か」「このAPIを使う権限があるか」を証明するために使用


###### 認証スキーム

| スキーム種類 | 形式 | 用途 | 一般度 |
|---------|------|------|--------|
| **Bearer** | `Bearer <token>` | OAuth 2.0、JWT | 一般的 |
| **Basic** | `Basic <base64>` | ユーザー名:パスワード | レガシー |
| **Digest** | `Digest <params>` | チャレンジ/レスポンス認証 | 稀 |

###### Basic認証との比較

| 項目 | Basic | Bearer |
|------|-------|--------|
| 送る情報 | ユーザー名:パスワード | トークン |
| エンコード | Base64（簡単に復号可能） | 暗号化署名付き |
| 有効期限 | なし（パスワード変更まで有効） | あり（通常1時間など） |
| 漏洩リスク | 高い | 低い（期限切れで無効化） |
| 現在の推奨 | ❌ 非推奨 | ✅ 推奨 |

###### Bearer認証スキーム

- トークンベースの認証方式
- 「Bearer」という名前は、**トークンを持っている者（bearer）に認可を与える**という意味に由来
- JWT(JSON Web Token)がもっとも一般的
  - `ヘッダー.ペイロード.署名` -> 例: `eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1c2VyMSJ9.signature`
    1. ヘッダー: アルゴリズムとトークンタイプ
    2. ペイロード: ユーザー情報や有効期限などのクレーム
    3. 署名: 改ざん検知用


```text
# 構造
Authorization: Bearer <トークン>

# 例
GET /api/data HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```