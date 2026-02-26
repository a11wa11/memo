## systemd

- 基本コマンド

```sh
systemctl start 対象サービス名
systemctl stop 対象サービス名
systemctl restart 対象サービス名
systemctl status 対象サービス名
systemctl enable 対象サービス名
systemctl disable 対象サービス名
```

```sh
# systemdの設定ファイルを再読み込み
systemctl daemon-reload

# 設定を編集したい場合
systemctl edit 対象サービス名

# 以下編集参考例
[Service]
Restart=always # 切断されると常時リスタート
RestartSec=10  # 切断後10秒後にリスタート

# 最終的にマージされた設定を確認
systemctl cat 対象サービス名

# 編集ソフトをvimで指定する
SYSTEMD_EDITOR=vim systemctl edit 対象サービス名
```

- 設定ファイル編集の流れ

```sh
# Step 1: 設定ファイルを編集
systemctl edit 対象サービス名
# → /etc/systemd/system/対象サービス名.service.d/override.conf が作成される

# Step 2: systemdに設定変更を認識させる
systemctl daemon-reload
# → systemdがoverride.confの内容をメモリに読み込む

# Step 3: サービスを再起動して反映
sudo systemctl restart対象サービス名
```
