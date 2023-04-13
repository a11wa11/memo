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
