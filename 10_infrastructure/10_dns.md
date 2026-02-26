# 名前解決

以下、LinuxシステムのDNS名前解決を管理するファイル

1. /etc/systemd/resolved.conf(メイン設定)            ※編集可
  - systemd-resolvedの動作を制御するメインファイル
2. /etc/systemd/resolved.conf.d/vpc.conf	(追加設定) ※編集可
  - /etc/systemd/resolved.conf.d/ディレクトリ内のすべてのファイルが読み込まれる
3. /etc/resolv.conf(シンボリックリンク)
  - 1,2などを編集すると自動反映される(編集非推奨)
4. /run/systemd/resolve/resolv.conf(自動生成)
  - systemd-resolved が /etc/systemd/resolved.confを読み込んで生成する実ファイル

記述例

```/etc/systemd/resolved.conf
[Resolve]
DNS=10.0.0.2 20.0.0.2                   # DNS リゾルバーの IPアドレスリスト(優先順位順)
# ドメイン単位の DNS設定指定
Domains=ap-northeast-1.compute.internal # ap-northeast-1.compute.internalをDNSリストの1番目のリゾルバー(10.0.0.2)を使用する
```


###### systemd-resolved

- LinuxのシステムDNS名前解決サービス
- systemdの一部として動作するデーモン
- `/run/systemd/resolve/resolv.conf`を動的に生成・管理

```sh
systemctl status systemd-resolved
systemctl restart systemd-resolved
```

###### resolvectl

- DNS設定の確認・管理コマンド

```sh
# 現在の DNS設定を確認
resolvectl status

# 特定のドメイン名を解決テスト
resolvectl query ドメイン名

# DNS統計情報を表示
resolvectl statistics

# 統計情報をリセット
resolvectl reset-statistics

# tun0(1番目のトンベル)にDNSサーバーを設定。再起動すると元に戻る
resolvectl dns tun0 99.99.0.2(対象のDNSサーバー)

# tun0をデフォルトルートに設定(デフォルトルートが複数あればすべてのデフォルトルートに問い合わせて早い方を採用する)
resolvectl default-route tun0 true

# キャッシュをクリア
resolvectl flush-caches
```
