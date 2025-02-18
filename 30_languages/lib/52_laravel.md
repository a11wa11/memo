# laravel

## コマンド

```sh
# laravelの起動
php artisan serve

# laravelを起動し、ホストを0.0.0.0に設定して外部からの接続を受け付け。portは8000を指定
php artisan serve --host 0.0.0.0 --port 8000

# laravel使用可能コマンドの一覧
php artisan list

# laravelコマンドの内容表示
php artisan help コマンド名

# 設定のキャッシュ。 設定ファイルを1つのキャッシュファイルにまとめる。デプロイ後、設定が確定した時などに使用
php artisan config:cache

# キャッシュのクリア
## 全般的なキャッシュのクリア。 設定、ルート、ビューキャッシュ、およびコンパイル済みファイルをクリア。アプリケーション全体のキャッシュをリセットしたい時
php artisan optimize:clear # php artisan config:clearを含むわけではない
## 設定キャッシュのクリア。 設定のキャッシュをクリアする。設定ファイルを変更した後や開発中に設定をリセットしたい時
php artisan config:clear
## アプリケーションのデータキャッシュのみ削除
php artisan cache:clear

# ルーティング一覧
php artisan route:list

# デバッグ
php artisan tinker
php -r "echo getenv('環境変数名');" # 環境変数の確認
```
