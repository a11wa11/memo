# ansible

## 特徴

* 冪等生 >>> 「ある操作を何度実行しても常に結果が同じになる」性質のこと
  * `cp fileA /tmp` を実行したいときの例
    * 冪等生なしの場合
      * そのまま`cp fileA /tmp` を実行し、もし`/tmp/fileA`が実行前にあったら再コピーして、無駄な処理が増える
    * 冪等生ありの場合
      * fileAがない時だけ、`cp fileA /tmp` 実行するイメージ

## インストール

```sh
# CentOS7にて動作確認済
yum install epel-release
yum install ansible --enablerepo=epel-testing

# amazon-linuxにて動作確認済
amazon-linux-extras install epel
yum install -y ansible

# Ubuntu 18.04, 20.04にて動作確認済
apt update
apt install -y ansible
```

## 構成ファイル

* 主に`ansible.cfg`で構成内容を設定するがどの`ansible.cfg`を採用されるかは優先度がある
* 上(1)が優先度低く、下(4)に行くにつれて優先度が高くなる
  1. `etc/ansible/ansible.cfg` # よくシステムでインストールされた際にデフォルトで作成される
  1. `~/.ansible.cfg`          # ユーザーのホームディレクトリ
  1. `./ansible.cfg`           # カレントディレクトリ
  1. `ANSIBLE_CONFIG`          # 環境変数で指定された構成ファイル　例 `export ANSIBLE_CONFIG=/home/test/my_ansible.cfg`

```sh
# ログの出力先を設定
log_path = /var/log/ansible.log
# ログの出力時に改行する
stdout_callback = yaml
```

## インベントリ

### ホスト

* `/etc/hosts`へ登録済の内容はインベントリファイルでも指定可能

```sh
$ cat /etc/hosts

192.168.0.0 controller
192.168.0.1 server1
192.168.0.2 server2
```

* `/etc/ansible/hosts` に対象のサーバを登録することで省略化、グループ化が可能

```host
[aws]
10.0.0.0 ansible_ssh_user=USERNAME ansible_ssh_private_key_file=~/.ssh/SAMPLE.pem
```

### インベントリの登録注意点

#### ansibleはsshで接続するため、認識していないホストは鍵確認で入力待ちになってしまうが、以下のいずれかの方法で無視できる

* 環境変数をコマンド前に入力する

```sh
ANSIBLE_HOST_KEY_CHECKING=False ansible all -m ping
```

* hostsファイルに以下のように記載

```hosts
[defaults]
inventory = hosts
host_key_checking = False
ansible_user=root
ansible_become=True
ansible_become_pass=password
ansible_port=2222
```

* ansibleで取得する変数を確認可能

```sh
ansible インベントリ -m setup

# hostsファイルなどに登録していないときにIPアドレスを指定して確認する場合
ansible -m setup -i '192.168.1.xxx,' all --private-key=SSHキーパス
```

* グループのホスト名を一覧表示する

```sh
ansible グループ名 --list-hosts
ansible ~.*1 --list-hosts # 正規表現で指定も可能
```

* インベントリの構成をyaml形式で一覧表示

```sh
ansible-inventory -i インベントリ --list --yaml
```

## ansibleコマンド

```sh
ansible 10.0.0.0 -m ping -i hosts --private-key ~/.ssh/SAMPLE.pem -u ec2-user

# 以下は /etc/ansible/hosts にインベントリを登録していれば上記と同じ動作
ansible 10.0.0.0 -m ping -i hosts
ansible aws -m ping -i hosts

# インベントリファイル内の特定の値(サブセット名)を抽出して指定する
ansible 10.0.0.0 -m ping -i hosts -l サブセット名
```

## playbook

* yml文法に間違いないかチェックする

```sh
ansible-playbook --syntax-check YAML_FILE_NAME.yml
```

* デバッグ実行

```sh
ansible-playbook SAMPLE.yml --step
```

* dry-run実行(実際には実行しない)

```sh
ansible-playbook SAMPLE.yml --check
ansible-playbook SAMPLE.yml -C      # 省略版
# shell,commandモジュールでは強制skipとなるため、該当の箇所は check_mode: no を利用する
```

* 時間算出

```sh
time ansible-playbook SAMPLE.yml
```

* 実行

```sh
# 基本実行の形
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス

# インベントリファイルを指定せず、直接ホストを指定する場合はカンマをつける
ansible-playbook -i ホスト名, playbook.ymlファイルのパス

# 下記のインベントリファイルの指定のサブセットのみ実行する
ansible-playbook -i inventoryファイルのパス -l サブセット名 playbook.ymlファイルのパス
[サブセット1]
192.168.111.101
[サブセット2]
192.168.111.102
[サブセット3]
192.168.111.103
[サブセット4:children]
web
log

# 並列実行する台数を指定する(デフォルトは5)
ansible-playbook -i inventoryファイルのパス --forks=1 playbook.ymlファイルのパス # 1台ずつ実行
ansible-playbook -i inventoryファイルのパス -f 2 playbook.ymlファイルのパス      # 2台ずつ実行 　オプション省略版
```

* 一部タグのみ実行

```sh
# 指定したタグのみ実行
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス  --tags=タグ名
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス  -t "タグ名1,タグ名2"  # 省略版
# 指定したタグをスキップして実行
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス  --skip-tags="タグ名１,タグ名"

# どのタグやタスクが実行されるか表示する
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス  --list-tags   # 実行タグを表示
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス  --list-tasks  # 実行タスクを表示
```

### [プレイブックキーワード](https://docs.ansible.com/ansible/2.9/reference_appendices/playbooks_keywords.html)

### handler

* 変更があった場合、タスクの最後に1回だけ実行される。変更がない場合は実行されない

```yaml
  tasks:
    - name: Configure a MOTD (message of the day)
      copy:
        content: "{{ motd }}"
        dest: /etc/motd
      notify: MOTD changed # 呼び出すhandlerの名称を記載

  handlers: # 該当のtaskで変更箇所があった場合に実行してくれる
    - name: MOTD changed
      debug:
        msg: The MOTD was changed
```

### ロール

* 構成
  * defaults/: ロールのデフォルト設定を格納するフォルダ
  * files/: ロールで使用するファイルを格納するフォルダ
  * handlers/: ロールで使用するハンドラを格納するフォルダ
  * meta/: ロールに関するメタデータを格納するフォルダ
  * tasks/: ロールで実行するタスクを格納するフォルダ
  * templates/: ロールで使用するテンプレートを格納するフォルダ
  * vars/: ロールで使用する変数を格納するフォルダ
  * tests/: ロールのテストを格納するフォルダ

```sh
# ロールのテンプレート構成を作成
ansible-galaxy init ロール名
```

### よく使うモジュール

* ターゲットセクションで定義するモジュール
  * gather_facts →　デフォルトでyes。実行時間など図れるがその分処理時間がプラスされるため、不要であれば`false`で設定
  * become       → 実行ユーザーを定義
  * connection

* when
  * 条件分岐 -> 例 `when: ansible_architecture == 'x86_64'`
* block
  * `try except`のようにエラー処理
* ansible_distribution → 実行中のホストのOSのディストリビューションを示す変数(Amazon,Ubuntu,CentOSなど)
* with_items
  * リストで値を保持し、以下のように `{{変数名}} `で展開が可能

```yml
- name: Install ruby
  yum:
    name: "{{ item.name }}"
    state: "{{ item.state }}"
    enablerepo: "{{ enablerepo }}"
  with_items: "{{ yum_install }}"
  # with_items: "{{ yum_install + yum_install_plus }}" # ちなみに変数の型が同じなら複数の変数を組み合わせ可能
  tags: php
```

```yml
# 変数の参考値
yum_install:
  - {
      name: "php",
      state: "latest"
    }
  - {
      name: "ruby",
      state: "latest"
    }
  - {
      name: "python3",
      state: "latest"
    }
yum_install_plus:
  - {
      name: "mysql",
      state: "latest"
    }
```

#### [モジュール公式一覧](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

* [include](https://docs.ansible.com/ansible/2.9/modules/include_module.html#include-module)

* [debug](https://docs.ansible.com/ansible/2.9/modules/debug_module.html#debug-module)

| オプション名 | 説明　| 必須 | デフォルト値 |
| - | - | - | - |
| msg | 定義された文を表示する | × | Hello world! |
| var | 変数名をデバッグする(msg引数とお互いに排他的であり、msgと同時に使用できない) | × | |

* 変数などを表示したい時

```yml
 - name: Show contents
   debug:
     var: contents
```

* [get_url](https://docs.ansible.com/ansible/2.9/modules/get_url_module.html)
* [unarchive](https://docs.ansible.com/ansible/2.9/modules/unarchive_module.html)
* [lineinfile](https://docs.ansible.com/ansible/2.9/modules/lineinfile_module.html#lineinfile-module) → マッチした行を置換する(1行のみ)
* [copy](https://docs.ansible.com/ansible/2.9/modules/copy_module.html)
* [file](https://docs.ansible.com/ansible/2.9/modules/file_module.html)
* [shell](https://docs.ansible.com/ansible/2.9/modules/shell_module.html) → 　シェルを実行　※冪等製が保たれないので非推奨
* [script](https://docs.ansible.com/ansible/2.9/modules/script_module.html) → 対象のスクリプトを実行
* [service](https://docs.ansible.com/ansible/2.9/modules/service_module.html)
* [user](https://docs.ansible.com/ansible/2.9/modules/user_module.html)
* [pause](https://docs.ansible.com/ansible/2.9/modules/pause_module.html)
* [set_fact](https://docs.ansible.com/ansible/2.9/modules/set_fact_module.html) → 変数の設置
* [setup](https://docs.ansible.com/ansible/2.9/modules/setup_module.html) → ansibleコマンドで自動的に呼ばれる
* local_action
  * [wait_for](https://docs.ansible.com/ansible/2.9/modules/wait_for_module.html)

* ignore_errors

ignore_errosをyesにすると、そのタスクでエラーが発生しても無視して次のタスクに進むことができる。shellモジュールやcommandモジュールに使用する場合が多いかも

```yaml
- name: 必ず失敗するタスク
  command: /bin/false
  ignore_errors: yes

- debug:
    msg: '直前タスクの失敗の場合でも次に進みます'
```

### ログ出力

* [ansible実行時のログ出力先の指定と出力形式を指定して改行を見やすくする](https://tekunabe.hatenablog.jp/entry/2019/07/11/ansible_vallback_plugin_yaml)
  * `ansible.cfg`というファイルをansible実行するルートディレクトリに配置する
  * 環境毎に設定を変更したい場合は`ansible.cfg`を各ディレクトリに配置可能

```yaml
[defaults]
# ログの出力先を設定
log_path = /var/log/ansible.log
# ログの出力時に改行し、見やすくする
stdout_callback = yaml
```

### [リンター](https://ansible-lint.readthedocs.io/)

ansible-lintでバグ解析可能

*　インストール

```sh
pip install ansible-lint
```

*　使い方

```sh
ansible-lint 対象ファイル
ansible-lint  -p 対象ファイル # １行表示
```

* [参考リポジトリ1-diveintoansible-lab](https://github.com/spurin/diveintoansible-lab/blob/master/docker-compose.yaml)
* [参考リポジトリ2-diveintoansible](https://github.com/spurin/diveintoansible)
