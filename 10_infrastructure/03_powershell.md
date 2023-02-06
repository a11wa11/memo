# powershell

## 初期設定(セットアップ)画面でコマンドを使ってPCの電源をOFFにする方法

```powershell
shutdown /s /t 0
```

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
```

* wslの名前解決できない時の対処

https://zenn.dev/ekuinox/articles/520500939e5242a6393c
https://qiita.com/kkato233/items/1fc71bde5a6d94f1b982