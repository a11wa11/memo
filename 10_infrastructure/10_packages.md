# packages

- [packages](#packages)
  - [rclone](#rclone)

## rclone

クラウドストレージやリモートストレージを、ローカルファイルのように扱えるコマンドラインツール

- [AWS公式rclone記事](https://aws.amazon.com/jp/blogs/storage/migrate-data-from-dropbox-to-amazon-s3-using-rclone/)
- [rclone公式記事](https://rclone.org/remote_setup/)

```sh
# rclone設定を確認
## ~/.config/rclone/rclone.conf に設定ファイル格納
rclone config show

# rclone設定パスを表示
rclone config file

# 対話で設定する
rclone config
# n → dropbox → APIキー入力 → ブラウザで許可など

# コマンドのみでリモート設定(対話不要)
rclone config create リモート設定名 dropbox(タイプ指定) \
  client_id "APP_KEY" \
  client_secret "APP_SECRET" \
  token '{"access_token":"...","refresh_token":"...","expiry":"..."}' \
  --non-interactive

# コマンドのみで一部設定のみ更新(対話不要)
rclone config update dropbox-remote client_id=新しい値

# どんな機能に対応しているかを確認する
rclone backend features 接続タイプ名:

# dry-run
rclone ls 接続タイプ名:ディレクトリ名/ファイル名 --dry-run -v

# リモート接続先確認
rclone listremotes # 設定済接続先一覧

rclone ls 接続タイプ名:               # 全て確認 ディレクトリは非表示
rclone ls 接続タイプ名:ディレクトリ名   # ディレクトリなど指定
rclone lsf 接続タイプ名:              # ディレクトリも表示して確認
rclone lsf --format sp 接続タイプ名:  # ディレクトリも表示して確認
rclone lsd 接続タイプ名:              # ディレクトリのみ表示
rclone lsl 接続タイプ名:              # 詳細表示
rclone size 接続タイプ名:ディレクトリ名 # ファイル数と合計サイズを表示
rclone about 接続タイプ名:            # ストレージの使用量確認

# コピー
rclone copy ./ローカルパス s3:バケット名/パス --progress

# 移動
rclone move ./ローカルパス s3:バケット名/パス

# マウント
rclone mount gdrive: ~/mnt/gdrive # GoogleDriveをローカルにマウント

# ミラー
## --fast-list 差分チェック
rclone sync dropbox:/ s3:バケット名/パス --fast-list --progress 
rclone sync s3:バケット名/パス ./ローカルパス --progress # ローカルにもミラー可能

# その他オプション
--progress              # 進捗を常に表示
--stats 30s             # 標準出力間隔                                                  デフォルト1秒
--stats-one-line        # 進捗表示を1行にまとめてコンパクトに表示する                        デフォルト複数行

--checkers 4            # メタ情報チェックの並列数(チェックフェーズで差分あるか確認)            デフォルト8個
--transfers 4           # 同時転送ファイル数(チェックフェーズで必要となったものから随時転送開始)  デフォルト8個
--tpslimit 5.0          # Transactions Per Second(1秒ごとに何回APIコールするか)           デフォルト0 =　無制限
# tpslimit,checkers,transfersの関係性
## tpslimitは1秒あたりのAPIコールの最大値のため、checkers,transfersがtpslimitを上回ると待機になり、意味がない。
## tpslimit > checkers + transfers となるのが理想的

--retries 8             # rcloneコマンド自体失敗時のリトライ回数                           デフォルト3回
--retries-sleep 60s     #  --retriesの待機時間
--low-level-retries 20  # 個別のファイル転送やAPI呼び出し単位での再試行回数                  デフォルト10回

--max-duration 4h       # 4時間で強制停止

--max-size 100M         # 100MB未満のファイルのみ対象
--min-size 100M         # 100MB以下のファイルのみ対象

## タイプ別オプション
--dropbox-chunk-size 128M  # 大きくするとスループットやメモリ上昇だが転送速度早い デフォルト48M
--s3-chunk-size 128M       # 〃
--s3-upload-concurrency 8  # 1ファイルを何並列で上げるか

## コマンド例
### コピーコマンドで転送速度早め
rclone copy dropbox:/パス s3:バケット名/パス --dropbox-chunk-size 128M --transfers 8 --progress

### syncコマンドで転送速度早め
rclone sync dropbox:/ s3:バケット名/パス \
  --fast-list --transfers 16 --checkers 32 \
  --progress --stats 30s --stats-one-line \
  --dropbox-chunk-size 128M \
  --s3-upload-concurrency 8 --s3-chunk-size 128M \
  --retries 8 --low-level-retries 20
```
