# ansible

## 特徴
### 冪等生

* 「ある操作を何度実行しても常に結果が同じになる」性質のこと
  * `cp fileA /tmp` を実行したいときの例
    * 冪等生なしの場合
      * そのまま`cp fileA /tmp` を実行し、もし`/tmp/fileA`が実行前にあったら再コピーして、無駄な処理が増える
    * 冪等生ありの場合
      * fileAがない時だけ、`cp fileA /tmp` 実行するイメージ

### インストール

```
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

### 構成ファイル

* 主に`ansible.cfg`で構成内容を設定するがどの`ansible.cfg`を採用されるかは優先度がある
  1. `etc/ansible/ansible.cfg` # よくシステムでインストールされた際にデフォルトで作成される
  1. `~/.ansible.cfg`          # ユーザーのホームディレクトリ
  1. `./ansible.cfg`           # カレントディレクトリ
  1. `ANSIBLE_CONFIG`          # 環境変数で指定された構成ファイル　例 `export ANSIBLE_CONFIG=/home/test/my_ansible.cfg`
* 上(1)が優先度低く、下(4)に行くにつれて優先度が高くなる

### 設定

```
$ cat /etc/hosts

192.168.0.0 controller
192.168.0.1 server1
192.168.0.2 server2
```

### インベントリの登録

* `/etc/ansible/hosts` に対象のサーバを登録することで省略化、グループ化が可能

```
[aws]
10.0.0.0 ansible_ssh_user=USERNAME ansible_ssh_private_key_file=~/.ssh/SAMPLE.pem
```

* ansibleはsshで接続するため、認識していないホストは鍵確認で入力待ちになってしまうが、以下のいずれかの方法で無視できる

  * 環境変数をコマンド前に入力する
```
ANSIBLE_HOST_KEY_CHECKING=False ansible all -m ping
```
  * hostsファイルに以下のように記載

```
[defaults]
inventory = hosts
host_key_checking = False
ansible_user=root
ansible_become=True
ansible_become_pass=password
ansible_port=2222
```

### ansibleコマンド

```
ansible 10.0.0.0 -m ping -i hosts --private-key ~/.ssh/SAMPLE.pem -u ec2-user

# 以下は /etc/ansible/hosts にインベントリを登録していれば上記と同じ動作
ansible 10.0.0.0 -m ping -i hosts
ansible aws -m ping -i hosts

# グループのホスト名を一覧表示する
ansible グループ名 --list-hosts
ansible ~.*1 --list-hosts # 正規表現で指定も可能
```

## playbook

* yml文法に間違いないかチェックする

```
ansible-playbook --syntax-check YAML_FILE_NAME.yml
```

* デバッグ実行

```
ansible-playbook SAMPLE.yml --step
```

* 実行

```
ansible-playbook -i inventoryファイルのパス playbook.ymlファイルのパス
```

### よく使うモジュール

* when →　条件分岐
* block → `try except`のようにエラー処理
* ansible_distribution →　
* with_items →　リストで値を保持し、`{{var}}`で展開が可能

##### [モジュール公式一覧](https://docs.ansible.com/ansible/2.9/modules/modules_by_category.html)

* [include](https://docs.ansible.com/ansible/2.9/modules/include_module.html#include-module)

* [debug](https://docs.ansible.com/ansible/2.9/modules/debug_module.html#debug-module)

| オプション名 | 説明　| 必須 | デフォルト値 |
| - | - | - | - |
| msg | 定義された文を表示する | × | Hello world! |　
| var | 変数名をデバッグする(msg引数と意お互いに排他的である) | × | |
|  |  |

* [get_url]
* [unarchive]
* [lineinfile](https://docs.ansible.com/ansible/2.9/modules/lineinfile_module.html#lineinfile-module) → マッチした行を置換する

- shell　シェルを実行　※冪等製が保たれないので非推奨
- script 対象のスクリプトを実行
- service
- file
- user
- item
- pouse 
- set_fact 変数の設置
- setup # ansibleコマンドで自動的に呼ばれる
- local_action
  - wait_for


- hosts: localhost
  become: yes
  tasks:
    - name: ubuntuのセットアップ
      block:
      - name: アップグレード
        apt: upgrade=full
        register: result
      - debug: msg={{result}}

      - name: sudoコマンド実行時にパスワードを求められないようにする
        become: no
        command: whoami
        register: user_result
        check_mode: no
        changed_when: false
      
      - name: sudoコマンド実行時にパスワードを求められないようにする
        lineinfile: upgrade=full
          path: /etc/sudoers
          backup: yes
          insertafter: EOF
          line: "{{user_result.stdout}} ALL=(ALL) NOPASSWD:ALL"
        register: result
      - debug: msg={{result}}

      - name: 3-1. 日本語パックのインストール
        apt:
          name: language-pack-ja
          state: present
        resister: result
      - debug: msg={{result}}

      - name: システムロケールの日本語化準備
        command: locale
        resister: locale_result
        check_mode: no
        changed_when: false

      - name: システムロケールの日本語化準備
        command: locale
        resister: locale_result
        check_mode: no
        changed_when: false

      - name: システムロケールの日本語化実行
        command: update-locale LANG=ja_JP.UTF-8
        when: "'LANG=ja_JP.UTF-8' not in locale_result.stdout"
        resister: result
      - debug: msg={{result}}

      - name: 日本語マニュアルのインストール
        apt:
          name:
            - manpages-ja
            - manpages-ja-dev
          state: present
        resister: result
      - debug: msg={{result}}

      - name: windowsからwslへssh可能にする
        lineinfile:
          path: /etc/ssh/sshd_config
          backrefs: yes
          backup: yes
          regexp: '^PasswordAutentication no'
          line: PasswordAutentication yes
        resister: result
      - debug: msg={{result}}

      - name: windowsからwslへssh可能にする
        lineinfile:
          path: /etc/ssh/sshd_config
          backrefs: yes
          backup: yes
          regexp: '^PasswordAutentication no'
          line: PasswordAutentication yes
        resister: result
      - debug: msg={{result}}
