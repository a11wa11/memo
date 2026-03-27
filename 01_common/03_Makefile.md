# Makefile

- [Makefile](#makefile)
	- [構文](#構文)
	- [実行コマンド](#実行コマンド)
		- [実行例](#実行例)

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

-　デバッグ

```Makefile
# infoで変数や文字列を出力する。makeコマンド実行時に出力される
$(info 表示内容=$(変数名))
```

## 実行コマンド

```sh
make ターゲット        # 指定したターゲット内容を実行する
make                 # ターゲットを指定しなければ一番最初に記載のあるターゲットが自動で実行される
make -n              # ターゲットを内容を実行せずに出力（デバッグ）
```

### 実行例

```Makefile
# 変数定義
ENV := dev
NOW := $(shell date '+%Y%m%d-%H%M') # 現時刻を変数代入
## AWS情報
AWS_REGION := ap-northeast-1
ECR_REPOSITORY := $(ENV)-SAMPLE
LAMBDA_FUNCTION_NAME := $(ENV)_SAMPLE
IMAGE_NAME := $(ENV)-SAMPLE
IMAGE_TAG := latest
AWS_ACCOUNT_ID := $(shell aws sts get-caller-identity --query Account --output text 2>/dev/null)
ECR_REGISTRY := $(AWS_ACCOUNT_ID).dkr.ecr.$(AWS_REGION).amazonaws.com
ECR_IMAGE_URI := $(ECR_REGISTRY)/$(ECR_REPOSITORY):$(IMAGE_TAG)
ECR_IMAGE_URI_DATE := $(ECR_REGISTRY)/$(ECR_REPOSITORY):$(NOW)
CLUSTER_NAME := $(ENV)-ecs
CONTAINER_NAME := ecs

help: ## このヘルプメッセージを表示
	@echo "利用可能なmakeコマンド:"
	@echo "  make login  - 1. ECRにログイン"
	@echo "  make build  - 2. Dockerイメージをビルド"
	@echo "  make tag    - 3. Dockerイメージにタグを付ける"
	@echo "  make push   - 4. DockerイメージをECRにプッシュ"
	@echo "  make update - 5. Lambda関数を更新"
	@echo "  make deploy - 上記1~5をすべてを一連で実行"
	@echo "  make clean  - ローカルのDockerイメージを削除"
	@echo ""
	@echo "変数:"
	@echo "  AWS_ACCOUNT_ID=$(AWS_ACCOUNT_ID)"
	@echo "  ECR_REGISTRY=$(ECR_REGISTRY)"
	@echo "  ECR_IMAGE_URI=$(ECR_IMAGE_URI)"
	@echo "  NOW=$(NOW)"

login: ## ECRにログイン
	@echo "ECRにログイン..."
	aws ecr get-login-password --region $(AWS_REGION) | docker login --username AWS --password-stdin $(ECR_REGISTRY)

build: ## Dockerイメージをビルド
	@echo "Dockerイメージをビルド..."
	docker build -t $(IMAGE_NAME):$(IMAGE_TAG) .

tag: ## Dockerイメージにタグを付ける
	@echo "Dockerイメージにタグを付与..."
	docker tag $(IMAGE_NAME):$(IMAGE_TAG) $(ECR_IMAGE_URI)
	docker tag $(IMAGE_NAME):$(IMAGE_TAG) $(ECR_IMAGE_URI_DATE)

push: tag ## DockerイメージをECRにプッシュ
	@echo "DockerイメージをECRにプッシュ..."
	docker push $(ECR_IMAGE_URI)
	docker push $(ECR_IMAGE_URI_DATE)

update: ## Lambda関数を更新
	@echo "Lambda関数を更新..."
	aws lambda update-function-code --function-name $(LAMBDA_FUNCTION_NAME) --image-uri $(ECR_IMAGE_URI) --output text

clean: ## ローカルのDockerイメージを削除
	@echo "ローカルのDockerイメージを削除..."
	-docker rmi $(ECR_IMAGE_URI) 2>/dev/null || true
	-docker rmi $(IMAGE_NAME):$(IMAGE_TAG) 2>/dev/null || true
	-docker images $(ECR_REGISTRY)/$(ECR_REPOSITORY) -q | xargs docker rmi

whole: login build push ## 一連のECR更新手順を実行（ログイン→ビルド→プッシュ）
	@echo "buildからpushまで完了しました!"

deploy: login build push update ## 一連のデプロイ手順を実行（ログイン→ビルド→プッシュ→Lambda更新）
	@echo "デプロイが完了しました！"

exec: ## ECSタスクに接続 (例: make exec CLUSTER_NAME=my-cluster CONTAINER_NAME=my-container)
	@echo "ECSへ接続します..."
	@TASK_ARN=$$(aws ecs list-tasks --region $(AWS_REGION) --cluster $(CLUSTER_NAME) --query "taskArns[0]" --output text) && \
	aws ecs execute-command --region $(AWS_REGION) --cluster $(CLUSTER_NAME) --task $$TASK_ARN --container $(CONTAINER_NAME) --interactive --command "/bin/bash"

.PHONY: help login build tag push update deploy clean
```
