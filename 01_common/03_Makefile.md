# Makefile

- [Makefile](#makefile)
  - [構文](#構文)
  - [実行コマンド](#実行コマンド)

## 構文

- 基本文

```Makefile
# 基本的な記載の仕方
プログラム名: ソースファイル(省略可)
  ビルドのためのコマンドライン # 必ず行頭はタブでなければいけない

    # 以下代表的な記載例
  @ls -l           # @をコマンドの先頭につけるとコマンド内容が出力されない（実行結果は出力される）
  make 別ターゲット  # 別タスクを呼ぶことはできる
  -rm test         # コマンドの前に `-` をつけることでエラーになっても無視されて次に移る
  $@               # ターゲット名を表す変数

.PHONY: ターゲット名   # ターゲットはファイルは生成しないという事を明示する。複数指定可。
                     # ターゲット名と同じファイルが存在していたときにmakeコマンドが実行されなくなるため。
.PHONY: all          # allというターゲットはファイルなしと明示
all: ;               # all は何も実行しないということになり、誤動作も防止することができる
```

- 変数定義
  - `x = foo`でも可能だが`x := foo`が理解しやすい

```Makefile
x := foo      # echo &(x) で foo が出力される
y := $(x) bar

# 現時刻を変数代入
now := $(shell date '+%Y%m%d-%H%M')
# カレントディレクトリを変数代入
current_dir := $(shell pwd)
result_dir := $(current_dir)/taurus/results/$(now)
```

- 引数を渡す

```Makefile
build:
  echo "ENV is $(ENV)"
```

```sh
make build ENV=production
```

- カレントディレクトリの解釈
  - `cd`で移動しても次の行では元のディレクトリに戻るので注意

```Makefile
# pwd
/path/to/current

# ↑こういう状況だとする
cd /path/to/current/somewhere
pwd    # /path/to/current と出力。次の行では元のディレクトリに戻っている

cd /path/to/current/somewhere; pwd  # /path/to/current/somewhere と出力

cd /path/to/current/somewhere && \
    pwd                             # /path/to/current/somewhere と出力
```

- エラー処理

```Makefile
# Makefileの組込関数
error "only supported in dev environment."                # エラーを出力して即時終了
$(error "only supported in dev environment. ENV: $(ENV)") # 変数組み込みたい場合
```

## 実行コマンド

```sh
make ターゲット        # 指定したターゲット内容を実行する
make                 # ターゲットを指定しなければ一番最初に記載のあるターゲットが自動で実行される
make -n              # ターゲットを内容を実行せずに出力（デバッグ）
```
