# monitoring

- [monitoring](#monitoring)
  - [監視種類](#監視種類)
    - [外形監視](#外形監視)

## 監視種類

- 外形監視(synthetic monitoring / Black-box monitoring)
  - 死活監視
- 内部監視(White-box monitoring)
  - メトリクス監視
- ログ監視

### 外形監視

ユーザーの視点からサービスを外部から定期的にアクセス・検証する監視手法

- ウェブアプリに HTTP/HTTPS でアクセスしてレスポンスコードが 200 か？
- レイテンシ（応答時間）は一定以下か？
- ログインページ→ダッシュボード→ログアウトの流れが完了できるか？
- DNS解決やSSL証明書の有効期限は正常か？
