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
```

### デバッグ

- デバッグコマンド

```sh
php artisan tinker
php -r "echo getenv('環境変数名');" # 環境変数の確認
```

- デバッグバー(barryvdh/laravel-debugbar)

```sh
# インストール
composer require barryvdh/laravel-debugbar
# 起動
php artisan serve # 画面上にデバッグバーが表示される
# .envファイルで管理可能
# APP_DEBUG=trueの場合は自動で表示される。APP_DEBUG=falseの場合は表示されない
```

- dd
  - 「Dump and Die」の略
  - 指定した変数の内容を出力し、その時点でスクリプトの実行を停止

```php
$user = User::find(1);
dd($user);
ddd($user); // 詳細表示
```

## 設定

### 初期設定

- config/app.php
  - `locale`を`ja`に変更し、言語設定を日本語にする
  - `timezone`を`Asia/Tokyo`に変更し、タイムゾーンを日本時間にする

### 構成

- ファサード
  - 複雑な関係のクラス群を扱うための窓口
  - Auth(認証),DB(クエリビルダー),Hash(暗号化),Gate(認可),Log(ログ),Mail(メール),Route(ルーティング),Storage(ストレージ)などのファサードがよく使う
- Blade
  - Laravelに組み込まれているテンプレートエンジン
    - テンプレートの継承: @extendsや@section、@yieldディレクティブを使用して、テンプレートの継承やセクションの定義が行える
    - 簡潔な構文: @ifや@foreachなどのディレクティブにより、条件分岐やループ処理をシンプルに記述できる
    - 直接的なPHPコードの使用: Bladeはテンプレート内で直接PHPコードを記述することを許可しており、柔軟な開発が可能

## データベース

- DBから情報を取得するには主に2種類ある
  - Eloquent
    - `Test::all();`のように モデル名::メソッドで書く
    - `use App\Models\Test`
    - コレクション型(配列を拡張したlaravel独自の型)
  - クエリビルダー
    - SQLに近い構文で記述可能
    - `DB::table('sample')where('test', '=', 'aaa')->select('id', 'test')->get();
    - `use Illuminate\Support\Facades\DB`
  - Eloquentとクエリビルダーでは若干クエリビルダーが速的に速いと言われるが複数テーブルの連携だったりクエリ分割などの機能はEloquentで使える
  - getメソッドなどをつけないとQueryBuilder型(SQL文を途中まで書いていて未確定のような状態)になっていることがあるのでよく確認すること

## 参考

- [Readouble](https://readouble.com/)
