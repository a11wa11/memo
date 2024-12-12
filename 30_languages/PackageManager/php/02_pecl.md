# PECL

- 概要

composerは、PHPプロジェクトの依存関係（ライブラリやパッケージ）を管理するためのツールであるのに対して、PECLはPHP拡張モジュールを単体でインストールし、主にPHPのコア機能を拡張したり、新しい機能を追加したりするためのツール

```sh
# インストール
pecl install パッケージ名
# バージョンを指定してインストール
pecl install パッケージ名-バージョン名
# アンインストール
pecl uninstall パッケージ名
#　一覧表示
pecl list
# インストール可能なすべてのパッケージを表示
pecl list-all
# パッケージの詳細情報を確認
pecl info パッケージ名
# 更新
pecl upgrade パッケージ名
# 全更新
pecl upgrade-all
```

## 参考

- [公式サイト](https://pecl.php.net)でインストール可能なパッケージのバージョンなど確認可能
- [php拡張モジュール](https://www.php.net/manual/ja/funcref.php)
