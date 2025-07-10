# powershell

## 初期設定(セットアップ)画面でコマンドを使ってPCの電源をOFFにする方法

```powershell
# /t "timeout"の略で、シャットダウンまでの待機時間を指定
# /s /sは"shutdown"の略
shutdown /s /t 0
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

- エクスプローラーからLunuxファイルにアクセスしたい場合、`\\wsl.localhost` に移動

* wslの名前解決できない時の対処

* [参考1](https://zenn.dev/ekuinox/articles/520500939e5242a6393c)
* [参考2](https://qiita.com/kkato233/items/1fc71bde5a6d94f1b982)
