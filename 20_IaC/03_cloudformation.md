# cloudformation

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: Create test-servers # 説明文を書ける
Parameters:
  # 使用する秘密鍵の設定
  KeyPair:
    Description: Pick EC2 key-pair
    Type: AWS::EC2::KeyPair::KeyName

  VpcCidr:
    Type: String
    Default: 99.99.0.0/16

  SubnetCidr:
    Description: WebServer EC2 subnet cidr
    Type: String
    Default: 99.99.0.0/24
    AllowedValues:
    - 99.99.0.0/24
    - 99.99.1.0/24
    - 99.99.2.0/24
    - 99.99.3.0/24
    ConstraintDescription: must be a valid EC2 subnet cidr

  InstanceType:
    Description: WebServer EC2 instance type
    Type: String
    Default: t2.micro
    AllowedValues:
    - t1.micro
    - t2.nano
    - t2.micro
    - t2.small
    - t2.medium
    - t2.large
    ConstraintDescription: must be a valid EC2 instance type

  Value:
    Type: String

Resources:
  #VPC
  MyVpc:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCidr
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}
  # セキュリティグループ
  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: !Sub aws-${AWS::Region}-${Value}
      GroupName: !Sub aws-${AWS::Region}-${Value}
      VpcId: !Ref MyVpc
      # アウトバウンド
      SecurityGroupEgress:
        - CidrIp: 0.0.0.0/0
          IpProtocol: -1
      # インバウンド
      SecurityGroupIngress:
        - CidrIp: 0.0.0.0/0
          IpProtocol: tcp
          FromPort: 0
          ToPort: 65532
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}

  # サブネット
  MySubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVpc
      AvailabilityZone: !Select
        - 0
        - Fn::GetAZs: !Ref 'AWS::Region' # defaultサブネットのあるAZを配列で返すので無い場合はエラーになる
      CidrBlock: !Ref SubnetCidr
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}
    
  # インターネットゲートウェイ
  MyInternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}

  # VPCにインターネットゲートウェイをアタッチ
  GatewayToInternet:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVpc
      InternetGatewayId: !Ref MyInternetGateway

  # VPC内ルートテーブル作成
  MyRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVpc
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: GatewayToInternet
    Properties:
      RouteTableId: !Ref MyRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref MyInternetGateway

  # サブネット関連付け
  MySubnetRouteTableAssociation1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MySubnet
      RouteTableId: !Ref MyRouteTable

  # NACL
  PublicNetworkAcl:
    Type: AWS::EC2::NetworkAcl
    Properties:
      VpcId: !Ref MyVpc
      Tags:
        - Key: Name
          Value: !Sub aws-${AWS::Region}-${Value}

  # NACLルール
  PublicNetworkAclEntry20:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: false # インバウンドの場合false
      NetworkAclId: !Ref PublicNetworkAcl
      Protocol: 6
      RuleAction: allow
      RuleNumber: 20
      PortRange:
        From: 22
        To: 22

  InboundRule21:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: false # インバウンドの場合false
      NetworkAclId: !Ref PublicNetworkAcl
      Protocol: 6
      RuleAction: allow
      RuleNumber: 21
      PortRange:
        From: 22
        To: 22

  InboundRule100:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: false # インバウンドの場合false
      NetworkAclId: !Ref PublicNetworkAcl
      Protocol: -1
      RuleAction: allow
      RuleNumber: 100

  OutboundRule10:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: true # インバウンドの場合false
      NetworkAclId: !Ref PublicNetworkAcl
      Protocol: 6
      RuleAction: deny
      RuleNumber: 10
      PortRange:
        From: 25
        To: 25

  OutboundRule10:
    Type: AWS::EC2::NetworkAclEntry
    Properties:
      CidrBlock: 0.0.0.0/0
      Egress: true # インバウンドの場合false
      NetworkAclId: !Ref PublicNetworkAcl
      Protocol: -1
      RuleAction: deny
      RuleNumber: 100

  # サブネット関連付け
  PublicSubnetNetworkAclAssociation1a:
    Type: AWS::EC2::SubnetNetworkAclAssociation
    Properties:
      SubnetId: !Ref MySubnet
      NetworkAclId: !Ref PublicNetworkAcl

  # EC2インスタンス
  # Amazon Linux 64bit
  EC2AmazonLinux:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0c2b8ca1dad447f8a
      SecurityGroupIds:
        - !Ref MySecurityGroup
      SubnetId: !Ref MySubnet
      InstanceType: t2.micro
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Sub us-east1_AmazonLinux

  # CentOS 7 64bit
  EC2Centos7:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0affd4508a5d2481b
      SecurityGroupIds:
        - !Ref MySecurityGroup
      SubnetId: !Ref MySubnet
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Sub us-east1_Centos7

  # Windows2016
  EC2Windows2016:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0b17e49efb8d755c3
      SecurityGroupIds:
        - !Ref MySecurityGroup
      SubnetId: !Ref MySubnet
      InstanceType: t2.micro
      KeyName: !Ref KeyPair
      Tags:
        - Key: Name
          Value: !Sub us-east1_Windows2016
      UserData:
        Fn::Base64:
          Fn::Sub : |
            <powershell>
            mkdir C:\work
            cd C:\work
            [Net.ServicePointManager]:: SecurityProtocol = [Net.SecurityProtocolType]::Tls12
            Invoke-WebRequest -Uri https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1 -OutFile ConfigureRemotingForAnsible.ps1
            powershell -ExecutionPolicy RemoteSigned .\ConfigureRemotingForAnsible.ps1 -SkipNetworkProfileCheck
            # powershell -ExecutionPolicy RemoteSigned .\ConfigureRemotingForAnsible.ps1 # これ使ってた
            </powershell>
```
