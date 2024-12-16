# Mac

## コマンド

- Macのスワップメモリに関する詳細を表示

```sh
$ sysctl vm.swapusage
vm.swapusage: total = 2048.00M  used = 1462.50M  free = 585.50M  (encrypted)
```

- macOSで強制的に非アクティブメモリを解放

```sh
sudo purge
```

- otool
  - macOSに標準でインストールされている
  - バイナリファイルの依存関係を確認する

```sh
otool -L ~/.asdf/installs/php/8.1.29/bin/php
```
