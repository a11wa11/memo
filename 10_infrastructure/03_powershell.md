# powershell

## 設定

### 初期設定(セットアップ)画面でコマンドを使ってPCの電源をOFFにする方法

```powershell
# /t "timeout"の略で、シャットダウンまでの待機時間を指定
# /s /sは"shutdown"の略
shutdown /s /t 0
```

### プロンプト編集

```
# powershellで実行
## 親フォルダを作成(存在していれば何もしない)
New-Item -ItemType Directory -Path (Split-Path $PROFILE) -Force | Out-Null

## Microsoft.PowerShell_profile.ps1をメモ帳で編集
notepad $PROFILE

# ファイル編集
## 以下の内容を保存
function prompt {
    "$($executionContext.SessionState.Path.CurrentLocation)`n> "
}

# powershell表記変更確認
PS C:\Users\XXXX\Documents
> 
```

- 起動時の開始パス指定

```
## Microsoft.PowerShell_profile.ps1をメモ帳で編集
notepad $PROFILE

# ファイル編集
## 以下の内容を保存
Set-Location "C:\Work" # 好きな開始パス
```


## コマンド例

- 基本(ls)

```powershell
# ディレクトリのみ
ls -Directory

# 再帰的
ls -Recurse
ls -Recurse -Depth 2  # 深さ制限

# 絞り込み
ls *.txt
```

- 環境変数設定

```powershell
# 現セッションのみ
$env:HTTP_PROXY = "http://プロキシホスト:ポート番号"
$env:HTTPS_PROXY = "http://プロキシホスト:ポート番号"
```

- エイリアス設定

```powershell
# llでもlsを実行するように設定(現セッションのみ)
Set-Alias ll ls

# 永続化したい場合は$PROFILEに以下記述
function ll { ls }
```


### WSL

- [wslコマンド](https://learn.microsoft.com/ja-jp/windows/wsl/basic-commands)

```powershell
wsl -u root -- ~/sample.sh; sleep 3;wsl --shutdown; sleep 5;
wsl -- ~/sample.sh; sleep 3;wsl --shutdown; sleep 5;
wsl Ubuntu-20.04 -u root -- ~/sample.sh; sleep 3; --shutdown; sleep 5;

# wslの指定のディストリビューションを実行
wsl.exe Ubuntu

# wslの終了
wsl.exe --shutdown

# wslの一覧表示
wsl -l
wsl -l --verbose

# インストール可能なwslディストリビューション一覧
wsl.exe -l -o

# ディストリビューションをインストール
wsl.exe --install ディストリビューション名
```

```sh
# WSLからwindowsにアクセス
cd /mnt/c
```

- エクスプローラーからLinuxファイルにアクセスしたい場合、`\\wsl.localhost` に移動

- wslの名前解決できない時の対処

- [参考1](https://zenn.dev/ekuinox/articles/520500939e5242a6393c)
- [参考2](https://qiita.com/kkato233/items/1fc71bde5a6d94f1b982)

## CMD

```CMD
# コマンドプロンプトでAzureVMメタデータ取得
curl -H "Metadata:true" "http://169.254.169.254/metadata/instance/compute?api-version=2021-02-01"
```
