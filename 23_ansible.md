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
```

### 設定

```
$ cat /etc/hosts

192.168.0.0 controller
192.168.0.1 server1
192.168.0.2 server2
```

### ansibleコマンド

```
ansible 10.0.0.0 -m ping -i hosts --private-key ~/.ssh/SAMPLE.pem -u ec2-user

# 以下は /etc/ansible/hosts にインベントリを登録していれば上記と同じ動作
ansible 10.0.0.0 -m ping -i hosts
ansible aws -m ping -i hosts
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

### インベントリの登録

* `/etc/ansible/hosts` に対象のサーバを登録することで省略化、グループ化が可能

```
[aws]
10.0.0.0 ansible_ssh_user=USERNAME ansible_ssh_private_key_file=~/.ssh/SAMPLE.pem
```

* `/etc/ansible/hosts` に対象のサーバを登録することで省略化、グループ化が可能



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