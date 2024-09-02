# deployer

## 概要

PHPで作られたCLIデプロイツール

[公式ドキュメント](https://deployer.org/docs/6.x/api)

## インストール

```sh
composer require deployer/deployer --dev
composer install
```

## コマンド

```sh
# デプロイ
php vendor/bin/dep deploy ホスト名 or 環境名

# ロールバック
php vendor/bin/dep rollback
```

### 構文

```php
// 設定
// プロジェクト名
set('application', 'プロジェクト名');
// リポジトリ名
set('repository', 'リポジトリ URL');
// インベントリ
inventory('./dir/file.yml');

// デプロイ先にファイルをいくつ残すか
set('keep_releases', 3);


desc('タスクの説明');
// どのような処理をするか定義
task('test_task', function () {
    $result = run('cd /var/www/html; pwd');
    writeln("result: $result");
});
// deployタスクが終了した後に任意のタスクを実行する
after('deploy', '実行したいタスク名');
```
