# monitoring

- [monitoring](#monitoring)
  - [監視種類](#監視種類)
    - [外形監視(synthetics)](#外形監視synthetics)
  - [監視用語](#監視用語)
  - [newrelic](#newrelic)
    - [ログ監視](#ログ監視)
    - [トレース監視](#トレース監視)

## 監視種類

- 外形監視(synthetic monitoring / Black-box monitoring)
  - 死活監視
- 内部監視(White-box monitoring)
  - メトリクス監視
- ログ監視

### 外形監視(synthetics)

ユーザーの視点からサービスを外部から定期的にアクセス・検証する監視手法

- ウェブアプリに HTTP/HTTPS でアクセスしてレスポンスコードが 200 か？
- レイテンシ（応答時間）は一定以下か？
- ログインページ→ダッシュボード→ログアウトの流れが完了できるか？
- DNS解決やSSL証明書の有効期限は正常か？

## 監視用語

- オブザービリティ
  - 観測データ（ログ・メトリクス・トレース）を使って、システム内部で起きていることを外から正しく理解できる状態。
- synthetics
  - 人工的に作ったユーザー行動で監視する仕組み
- 分散トレーシング(Distributed Tracing)
  - 1つのリクエストが複数のサービスを跨ぐ際の「足跡」を追跡する仕組み
- APM(Application Performance Monitoring)
  - アプリケーションのパフォーマンスを監視する仕組み
  - New Relic APM、Datadog APMなどがある
- OpenTelemetry
  - 分散トレーシングにおいてTraceIDやSpanID,ParentSpanIDを保存するというルール
  - Trace＝リクエスト全体、Span=各サービスの処理,ParentSpanID=呼び出し元SpanID
    - Span: リクエストの一足跡。spanがあつまってリクエスト全体の旅路がわかるので、トレースの一部ともいえる
- OTLP(OpenTelemetry Protocol)
  - 「テレメトリデータを送受信するための標準プロトコル
- W3C Trace Context
  - サービス間ヘッダーでトレース情報を伝搬

## [newrelic](https://docs.newrelic.com/jp/docs/new-relic-solutions/get-started/intro-new-relic/)

- Logs in Context
  - アプリケーションログとトレース(分散トレーシング)を自動的に紐付ける機能
- NRQL(New Relic Query Language)
  - New Relic内のデータを検索したり集計したり可視化したりするための専用クエリ言語
  - SQLに似ている

### ログ監視

- [ログ転送手段一覧](https://docs.newrelic.com/jp/docs/logs/forward-logs/enable-log-management-new-relic/)
  - [ECS](https://docs.newrelic.com/jp/install/aws-logs/?service=ECS)
    - [ECSのログ転送選択肢](https://newrelic.com/blog/how-to-relic/forward-logs-from-amazon-ecs-to-new-relic)
  - [Lambda](https://docs.newrelic.com/jp/docs/serverless-function-monitoring/aws-lambda-monitoring/instrument-lambda-function/introduction-lambda/)
    - [NewRelic-log-ingestion-s3](https://docs.newrelic.com/docs/logs/forward-logs/aws-lambda-sending-logs-s3/)
- [AMPエージェント](https://docs.newrelic.com/docs/apm/new-relic-apm/getting-started/introduction-apm/)

### トレース監視
