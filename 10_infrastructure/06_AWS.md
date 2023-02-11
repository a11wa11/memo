# AWS

```sh
effect →　どういう権限を設定(許可、否認)するか
action →　AWSサービスに対して実行できるAPI action誰が何を許可するかの何をの部分
resource → どの対象か　誰が何を許可するかの誰がの部分
インラインポリシー → 1つのIAMユーザなど、1対1でのアタッチしかできないポリシー
```

* [CloudTrail](https://aws.amazon.com/jp/cloudtrail/pricing/)

## VPC

```sh
VPCで設定できるサブネットマスクは16〜28ビットの範囲
VPCはサブネットマスク16ビット、サブネット24ビットが推奨されている

パブリックサブネットにするには対象サブネットのルートテーブルにインターネットゲートウェイを紐付けなければならない
```

## amazon-linux

```sh
# amazon-linuxでのEPEL追加
amazon-linux-extras install -y epel

# amazon-linux-extras のリスト確認
amazon-linux-extras
amazon-linux-extras list # list省略可能

```

## 予算管理

cloudwatchでは全体の予算管理のアラート、aws budgetでは個別のリソースの予算管理ができる
