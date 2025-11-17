# monitoring

- [monitoring](#monitoring)
  - [監視種類](#監視種類)
    - [外形監視](#外形監視)
  - [監視用語](#監視用語)
  - [newrelic](#newrelic)

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

## 監視用語

- synthetics
  - 人工的に作ったユーザー行動で監視する仕組み
- 分散トレーシング(Distributed Tracing)
  - 1つのリクエストが複数のサービスを跨ぐ際の「足跡」を追跡する仕組み
- APM(Application Performance Monitoring)
  - アプリケーションのパフォーマンスを監視する仕組み
  - New Relic APM、Datadog APMなどがある

## newrelic

- Logs in Context
  - アプリケーションログとトレース(分散トレーシング)を自動的に紐付ける機能
- NRQL(New Relic Query Language)
  - New Relic内のデータを検索したり集計したり可視化したりするための専用クエリ言語
  - SQLに似ている

