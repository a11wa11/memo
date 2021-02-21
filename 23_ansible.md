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

### インベントリの登録0

* `/etc/ansible/hosts` に対象のサーバを登録することで省略化、グループ化が可能

```
[aws]
10.0.0.0 ansible_ssh_user=ec2-user ansible_ssh_private_key_file=~/.ssh/SAMPLE.pem
```




### よく使うモジュール

- service
- file
- user
- var
- item 
- when 条件文期
- pouse 
- set_fact 変数の設置
- setup # ansibleコマンドで自動的に呼ばれる
- ansible_distribution
- lineinfile
- shell
- local_action
  - wait_for