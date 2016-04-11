# このハンズオンについて

- このハンズオンでは、Redshiftのクラスターとそのクラスタに対してクエリを発行するインスタンスの作成を実施します。
- クエリの発行には、「psql」を利用します。本手順では、Amazon Linux上へのインストールと利用方法は説明します。


# 前提条件

## バージョン確認

このハンズオンは以下のバージョンで動作確認を行いました。

```
aws --version
```

```
aws-cli/1.10.19 Python/2.7.10 Linux/4.4.5-15.26.amzn1.x86_64 botocore/1.4.10
```

## 必要な権限

作業にあたっては、以下の権限を有したIAMユーザもしくはIAMロールを利用してください。

- EC2に対するフルコントロール権限
- RedShiftに関するフルコントロール権限
- IAMに関するフルコントロール権限
- S3に関するフルコントロール権限
- STSに関するフルコントロール権限

# 0. 準備

## リージョンを指定

```
export AWS_DEFAULT_REGION='ap-northeast-1'
```

## 資格情報を確認

```
aws configure list
```

```
Name                    Value             Type    Location
----                    -----             ----    --------
profile                <not set>             None    None
access_key     ****************RDPA         iam-role
secret_key     ****************9GA8         iam-role
region           ap-northeast-1              env    AWS_DEFAULT_REGION
```

# 1. VPCの作成

## VPCのCIDRを指定

```
CIDR_BLOCK='10.0.0.0/16'
```

## パラメータを確認

```
cat << ETX

   VPC_CIDR_BLOCK: "${CIDR_BLOCK}"

ETX
```

```

   VPC_CIDR_BLOCK: "10.0.0.0/16"

```

## VPCを作成

```
VPC_ID=`aws ec2 create-vpc --cidr-block ${CIDR_BLOCK} --query Vpc.VpcId | sed s/\"//g` && echo ${VPC_ID}
```

```
vpc-********
```

## 作成したVPCの確認

```
aws ec2 describe-vpcs --vpc-ids ${VPC_ID}
```

```
{
    "Vpcs": [
        {
            "VpcId": "vpc-********",
            "InstanceTenancy": "default",
            "State": "available",
            "DhcpOptionsId": "dopt-********",
            "CidrBlock": "10.0.0.0/16",
            "IsDefault": false
        }
    ]
}
```

# 2. Internet Gatewayの作成、アタッチ

## インターネットゲートウェイの作成

```
IGW_ID=`aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId | sed s/\"//g` && echo ${IGW_ID}
```

```
igw-********
```

## 作成したInternet Gatewayを確認

```
aws ec2 describe-internet-gateways --internet-gateway-ids ${IGW_ID}
```

```
{
    "InternetGateways": [
        {
            "Tags": [],
            "InternetGatewayId": "igw-********",
            "Attachments": []
        }
    ]
}
```

## パラメータを確認

```
cat << ETX

   VPC_ID: "${VPC_ID}"
   IGW_ID: "${IGW_ID}"

ETX
```

```

   VPC_ID: "vpc-********"
   IGW_ID: "igw-********"

```

## VPCにインターネットゲートウェイをアタッチ

```
aws ec2 attach-internet-gateway --internet-gateway-id ${IGW_ID} --vpc-id ${VPC_ID}
```

```
(返値なし)
```

## アタッチした結果を確認

```
aws ec2 describe-internet-gateways --internet-gateway-ids ${IGW_ID}
```

```
{
    "InternetGateways": [
        {
            "Tags": [],
            "InternetGatewayId": "igw-********",
            "Attachments": [
                {
                    "State": "available",
                    "VpcId": "vpc-********"
                }
            ]
        }
    ]
}
```

# 3. Subnetの作成

Redhshiftのクラスターを作成する際、作成先のSubnetをSubnet Groupとして指定します。
RedhshiftにおけるSubnet Groupは、1つ以上のSubnetを含んでいる必要があります。


## サブネットを指定

```
CIDR_BLOCK_SUBNET_A='10.0.0.0/24'
```

## パラメータを確認

```
cat << ETX

   VPC_ID: "${VPC_ID}"
   Subnet_CIDR_BLOCK_on_${AWS_DEFAULT_REGION}a: "${CIDR_BLOCK_SUBNET_A}"

ETX
```

```

   VPC_ID: "vpc-ef43168a"
   Subnet_CIDR_BLOCK_on_ap-northeast-1a: "10.0.0.0/24"

```

## サブネットを作成

```
SUBNET_A_ID=`aws ec2 create-subnet --vpc-id ${VPC_ID} --cidr-block ${CIDR_BLOCK_SUBNET_A} --availability-zone ${AWS_DEFAULT_REGION}a --query Subnet.SubnetId | sed s/\"//g` && echo ${SUBNET_A_ID}
```

## 作成したサブネットを確認

```
aws ec2 describe-subnets --subnet-ids ${SUBNET_A_ID}
```

```
{
    "Subnets": [
        {
            "VpcId": "vpc-********",
            "CidrBlock": "10.0.0.0/24",
            "MapPublicIpOnLaunch": false,
            "DefaultForAz": false,
            "State": "available",
            "AvailabilityZone": "ap-northeast-1a",
            "SubnetId": "subnet-********",
            "AvailableIpAddressCount": 251
        }
    ]
}
```

# 4. Route Tableの編集

パブリックサブネットを作成するために、デフォルトのルートテーブルを編集します。

## 作成したVPCのデフォルトのルートテーブルIDを確認

```
ROUTE_TABLE_ID=`aws ec2 describe-route-tables --query RouteTables[?VpcId==\'${VPC_ID}\'].RouteTableId | jq ".[]" | sed s/\"//g` && echo ${ROUTE_TABLE_ID}
```

## パラメータを確認

```
cat << ETX

   RouteTable_ID: "${ROUTE_TABLE_ID}"
   InternetGateat_ID: "${IGW_ID}"

ETX
```

```

   RouteTable_ID: "rtb-********"
   InternetGateat_ID: "igw-********"

```

## デフォルトのルートテーブルにデフォルトルートを追加

```
aws ec2 create-route --route-table-id ${ROUTE_TABLE_ID} --destination-cidr-block '0.0.0.0/0' --gateway-id ${IGW_ID}
```

```
{
    "Return": true
}
```

## ルートテーブルに追加したルートを確認

```
aws ec2 describe-route-tables --query RouteTables[?VpcId==\'${VPC_ID}\']
```

```
[
    {
        "Associations": [
            {
                "RouteTableAssociationId": "rtbassoc-********",
                "Main": true,
                "RouteTableId": "rtb-********"
            }
        ],
        "RouteTableId": "rtb-********",
        "VpcId": "vpc-********",
        "PropagatingVgws": [],
        "Tags": [],
        "Routes": [
            {
                "GatewayId": "local",
                "DestinationCidrBlock": "10.0.0.0/16",
                "State": "active",
                "Origin": "CreateRouteTable"
            },
            {
                "GatewayId": "igw-********",
                "DestinationCidrBlock": "0.0.0.0/0",
                "State": "active",
                "Origin": "CreateRoute"
            }
        ]
    }
]
```

# 5. Sucurity Groupの作成、設定

## Security Groupの名前を設定（Amazon Linux用）

```
SG_GROUP_NAME_SSH='SSH'
SG_DESCRIPTION_SSH='JAWS-UG CLI at Co-Edo'
```

## パラメータの確認

```
cat << ETX

   SG_GROUP_NAME_SSH: ${SG_GROUP_NAME_SSH}
   SG_DESCRIPTION_SSH: ${SG_DESCRIPTION_SSH}

ETX
```

```

   SG_GROUP_NAME_SSH: SSH
   SG_DESCRIPTION_SSH: JAWS-UG CLI at Co-Edo

```

## ユーザ作成用のEC2インスタンスに設定するSecurity Groupを作成

```
SG_ID_SSH=`aws ec2 create-security-group --group-name ${SG_GROUP_NAME_SSH} --description "${SG_DESCRIPTION_SSH}" --vpc-id ${VPC_ID} --query GroupId | sed s/\"//g` && echo ${SG_ID_SSH}
```

```
sg-********
```


## 作成したSecurity Groupを確認

（アウトバウンドの通信のみ全て許可する設定のみ、がデフォルト）

```
aws ec2 describe-security-groups --group-ids ${SG_ID_SSH}
```

```
{
    "SecurityGroups": [
        {
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "UserIdGroupPairs": [],
                    "PrefixListIds": []
                }
            ],
            "Description": "JAWS-UG CLI at Co-Edo",
            "IpPermissions": [],
            "GroupName": "SSH",
            "VpcId": "vpc-********",
            "OwnerId": "************",
            "GroupId": "sg-********"
        }
    ]
}
```

## インバウンドのルールを追加

ssh接続を許可します。

接続元のIPアドレスを制限したい場合には、確認君などでIPアドレスを確認してください。
http://www.ugtop.com/spill.shtml

```
aws ec2 authorize-security-group-ingress --group-id ${SG_ID_SSH} --protocol 'tcp' --port 22 --cidr 0.0.0.0/0
```

## 追加されたルールを確認

```
aws ec2 describe-security-groups --group-ids ${SG_ID_SSH}
```

```
{
    "SecurityGroups": [
        {
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "UserIdGroupPairs": [],
                    "PrefixListIds": []
                }
            ],
            "Description": "JAWS-UG CLI at Co-Edo",
            "IpPermissions": [
                {
                    "PrefixListIds": [],
                    "FromPort": 22,
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "ToPort": 22,
                    "IpProtocol": "tcp",
                    "UserIdGroupPairs": []
                }
            ],
            "GroupName": "SSH",
            "VpcId": "vpc-********",
            "OwnerId": "************",
            "GroupId": "sg-********"
        }
    ]
}
```



# 6. Key Pairの作成

## KeyPairの名前および秘密鍵のファイル名を設定

```
KEY_PAIR_NAME='Redshift'
KEY_MATERIAL_FILE='redshift.pem'
```

## 同名のKey Pairが存在しないことを確認

```
aws ec2 describe-key-pairs --key-names ${KEY_PAIR_NAME}
```

```
A client error (InvalidKeyPair.NotFound) occurred when calling the DescribeKeyPairs operation: The key pair 'Redshift' does not exist
```

## 同名の秘密鍵ファイルが存在しないことを確認

```
ls -al ~/.ssh | grep ${KEY_MATERIAL_FILE}
```

```
（返値無し）
```

## パラメータの確認

```
cat << ETX

   KEY_PAIR_NAME: ${KEY_PAIR_NAME}
   KEY_MATERIAL_FILE: ${KEY_MATERIAL_FILE}

ETX
```


## KeyPairの作成


```
aws ec2 create-key-pair --key-name ${KEY_PAIR_NAME} --query KeyMaterial --output text > ~/.ssh/${KEY_MATERIAL_FILE}
```

## Key Pairが作成されたことを確認

```
aws ec2 describe-key-pairs --key-names ${KEY_PAIR_NAME}
```

```
{
    "KeyPairs": [
        {
            "KeyName": "Redshift",
            "KeyFingerprint": "**:**:**:**:**:**:**:**:**:**:**:**:**:**:**:**:**:**:**:**"
        }
    ]
}
```

## 秘密鍵ファイルを確認

```
cat ~/.ssh/${KEY_MATERIAL_FILE}
```

## 権限の変更

```
chmod 600 ~/.ssh/${KEY_MATERIAL_FILE}
```


# 7. インスタンスプロファイルの作成

EC2インスタンス上でIAMユーザの作成と権限の付与を行います。
そのための権限付与をここで行います。

## 同名のロールがないことを確認

```
ROLE_NAME='redshift-role'
aws iam get-role --role-name ${ROLE_NAME}
```

```
A client error (NoSuchEntity) occurred when calling the GetRole operation: The role with name redshift-role cannot be found.
```

## 信頼関係の定義

```
TRUST_POLICY_FILE='Trust-Policy.json'

cat << EOF > ${TRUST_POLICY_FILE}
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
```

JSONファイルを検証

```
jsonlint -q ${TRUST_POLICY_FILE}
```

## IAMロールの作成

```
aws iam create-role --role-name ${ROLE_NAME} --assume-role-policy-document file://${TRUST_POLICY_FILE}
```

```
{
    "Role": {
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Action": "sts:AssumeRole",
                    "Principal": {
                        "Service": "ec2.amazonaws.com"
                    },
                    "Effect": "Allow",
                    "Sid": ""
                }
            ]
        },
        "RoleId": "A********************",
        "CreateDate": "2016-04-02T12:33:09.681Z",
        "RoleName": "redshift-role",
        "Path": "/",
        "Arn": "arn:aws:iam::************:role/redshift-role"
    }
}
```

## IAMロールにManaged Policyをアタッチ

```
IAM_POLICY_ARN='arn:aws:iam::aws:policy/IAMFullAccess'
aws iam attach-role-policy --role-name ${ROLE_NAME} --policy-arn ${IAM_POLICY_ARN}
RED_POLICY_ARN='arn:aws:iam::aws:policy/AmazonRedshiftFullAccess'
aws iam attach-role-policy --role-name ${ROLE_NAME} --policy-arn ${RED_POLICY_ARN}
S3_POLICY_ARN='arn:aws:iam::aws:policy/AmazonS3FullAccess'
aws iam attach-role-policy --role-name ${ROLE_NAME} --policy-arn ${S3_POLICY_ARN}
```

```
（返値無し）
```

## IAM Roleにポリシーがアタッチされたことを確認

```
aws iam list-attached-role-policies --role-name ${ROLE_NAME}
```

```
{
    "AttachedPolicies": [
        {
            "PolicyName": "IAMFullAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/IAMFullAccess"
        },
        {
            "PolicyName": "AmazonS3FullAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonS3FullAccess"
        },
        {
            "PolicyName": "AmazonRedshiftFullAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonRedshiftFullAccess"
        }
    ]
}
```

## 同名のインスタンスプロファイルが存在しないことを確認

```
aws iam get-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
A client error (NoSuchEntity) occurred when calling the GetInstanceProfile operation: Instance Profile redshift-role cannot be found.
```

## インスタンスプロファイルを作成

```
aws iam create-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
{
    "InstanceProfile": {
        "InstanceProfileId": "A********************",
        "Roles": [],
        "CreateDate": "2016-04-02T12:35:07.689Z",
        "InstanceProfileName": "redshift-role",
        "Path": "/",
        "Arn": "arn:aws:iam::************:instance-profile/redshift-role"
    }
}
```

## インスタンスプロファイルが作成されたことを確認

```
aws iam get-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
{
    "InstanceProfile": {
        "InstanceProfileId": "A********************",
        "Roles": [],
        "CreateDate": "2016-04-02T12:35:07Z",
        "InstanceProfileName": "redshift-role",
        "Path": "/",
        "Arn": "arn:aws:iam::************:instance-profile/redshift-role"
    }
}
```

## インスタンスプロファイルにIAM Roleを追加

```
aws iam add-role-to-instance-profile --instance-profile-name ${ROLE_NAME} --role-name ${ROLE_NAME}
```

```
（返値無し）
```

## インスタンスプロファイルにIAM Roleが追加されたことを確認

```
aws iam get-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
{
    "InstanceProfile": {
        "InstanceProfileId": "A********************",
        "Roles": [
            {
                "AssumeRolePolicyDocument": {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Action": "sts:AssumeRole",
                            "Principal": {
                                "Service": "ec2.amazonaws.com"
                            },
                            "Effect": "Allow",
                            "Sid": ""
                        }
                    ]
                },
                "RoleId": "A********************",
                "CreateDate": "2016-04-02T12:33:09Z",
                "RoleName": "redshift-role",
                "Path": "/",
                "Arn": "arn:aws:iam::************:role/redshift-role"
            }
        ],
        "CreateDate": "2016-04-02T12:35:07Z",
        "InstanceProfileName": "redshift-role",
        "Path": "/",
        "Arn": "arn:aws:iam::************:instance-profile/redshift-role"
    }
}
```

# 8. EC2インスタンスの作成

## Amazon Linuxの最新AMIのImage IDを確認

Amazon LinuxのAMIのうち、作成日が最新のAMIを利用します。

```
AMI_ID=`aws ec2 describe-images --owners amazon --query Images[?Name==\'amzn-ami-hvm-2016.03.0.x86_64-gp2\'].ImageId | jq ".[]" | sed s/\"//g` && echo ${AMI_ID}
```

## パラメータを確認

AWSアカウントのIDを取得

```
AWS_ID=`aws sts get-caller-identity --query Account | sed s/\"//g` && echo ${AWS_ID}
```

```
************
```

パラメータを確認

```
cat << ETX

   AWS_ID: ${AWS_ID}
   AMI_ID: "${AMI_ID}"
   KEY_PAIR_NAME: ${KEY_PAIR_NAME}
   SecurityGroup_ID: "${SG_ID_SSH}"
   Subnet_ID: "${SUBNET_A_ID}"
   Instance Profile ARN: arn:aws:iam::${AWS_ID}:instance-profile/${ROLE_NAME}

ETX
```

```

   AWS_ID: ************
   AMI_ID: "ami-f80e0596"
   KEY_PAIR_NAME: Redshift
   SecurityGroup_ID: "sg-********"
   Subnet_ID: "subnet-********"
   Instance Profile ARN: arn:aws:iam::************:instance-profile/redshift-role

```

## EC2インスタンスを作成

```
INSTANCE_ID=`aws ec2 run-instances --image-id ${AMI_ID} --key-name ${KEY_PAIR_NAME} --security-group-ids ${SG_ID_SSH} --instance-type 't2.micro' --subnet-id ${SUBNET_A_ID} --associate-public-ip-address --iam-instance-profile Arn=arn:aws:iam::${AWS_ID}:instance-profile/${ROLE_NAME} --query Instances[0].InstanceId | sed s/\"//g` && echo ${INSTANCE_ID}
```

```
i-0****************
```

## EC2インスタンスが作成されたことを確認

```
aws ec2 describe-instances --instance-ids ${INSTANCE_ID}
```

```
{
    "Reservations": [
        {
            "OwnerId": "************",
            "ReservationId": "r-0****************",
            "Groups": [],
            "Instances": [
                {
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "PublicDnsName": "",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    },
                    "EbsOptimized": false,
                    "LaunchTime": "2016-04-02T13:07:20.000Z",
                    "PublicIpAddress": "**.**.**.**",
                    "PrivateIpAddress": "10.0.0.10",
                    "ProductCodes": [],
                    "VpcId": "vpc-********",
                    "StateTransitionReason": "",
                    "InstanceId": "i-0****************",
                    "ImageId": "ami-f80e0596",
                    "PrivateDnsName": "ip-10-0-0-10.ap-northeast-1.compute.internal",
                    "KeyName": "Redshift",
                    "SecurityGroups": [
                        {
                            "GroupName": "SSH",
                            "GroupId": "sg-********"
                        }
                    ],
                    "ClientToken": "",
                    "SubnetId": "subnet-********",
                    "InstanceType": "t2.micro",
                    "NetworkInterfaces": [
                        {
                            "Status": "in-use",
                            "MacAddress": "**:**:**:**:**:**",
                            "SourceDestCheck": true,
                            "VpcId": "vpc-********",
                            "Description": "",
                            "Association": {
                                "PublicIp": "**.**.**.**",
                                "PublicDnsName": "",
                                "IpOwnerId": "amazon"
                            },
                            "NetworkInterfaceId": "eni-********",
                            "PrivateIpAddresses": [
                                {
                                    "Association": {
                                        "PublicIp": "**.**.**.**",
                                        "PublicDnsName": "",
                                        "IpOwnerId": "amazon"
                                    },
                                    "Primary": true,
                                    "PrivateIpAddress": "10.0.0.10"
                                }
                            ],
                            "Attachment": {
                                "Status": "attached",
                                "DeviceIndex": 0,
                                "DeleteOnTermination": true,
                                "AttachmentId": "eni-attach-********",
                                "AttachTime": "2016-04-02T13:07:20.000Z"
                            },
                            "Groups": [
                                {
                                    "GroupName": "SSH",
                                    "GroupId": "sg-********"
                                }
                            ],
                            "SubnetId": "subnet-********",
                            "OwnerId": "************",
                            "PrivateIpAddress": "10.0.0.10"
                        }
                    ],
                    "SourceDestCheck": true,
                    "Placement": {
                        "Tenancy": "default",
                        "GroupName": "",
                        "AvailabilityZone": "ap-northeast-1a"
                    },
                    "Hypervisor": "xen",
                    "BlockDeviceMappings": [
                        {
                            "DeviceName": "/dev/xvda",
                            "Ebs": {
                                "Status": "attached",
                                "DeleteOnTermination": true,
                                "VolumeId": "vol-********",
                                "AttachTime": "2016-04-02T13:07:20.000Z"
                            }
                        }
                    ],
                    "Architecture": "x86_64",
                    "RootDeviceType": "ebs",
                    "IamInstanceProfile": {
                        "Id": "A********************",
                        "Arn": "arn:aws:iam::************:instance-profile/redshift-role"
                    },
                    "RootDeviceName": "/dev/xvda",
                    "VirtualizationType": "hvm",
                    "AmiLaunchIndex": 0
                }
            ]
        }
    ]
}
```

## パブリックIPを確認

```
PUBLIC_IP_ADDRESS=`aws ec2 describe-instances --instance-ids ${INSTANCE_ID} --query Reservations[0].Instances[0].PublicIpAddress | sed s/\"//g` && echo ${PUBLIC_IP_ADDRESS}
```

```
**.**.**.**
```

## Security Groupの名前を設定（Redshiftクラスタ用）

```
SG_GROUP_NAME_REDSHIFT='Redshift'
SG_DESCRIPTION_REDSHIFT='JAWS-UG CLI at Co-Edo'
```

## パラメータの確認

```
cat << ETX

   SG_GROUP_NAME: ${SG_GROUP_NAME_REDSHIFT}
   SG_DESCRIPTION: ${SG_DESCRIPTION_REDSHIFT}

ETX
```

```

   SG_GROUP_NAME: Redshift
   SG_DESCRIPTION: JAWS-UG CLI at Co-Edo

```

## ユーザ作成用のEC2インスタンスに設定するSecurity Groupを作成

```
SG_ID_REDSHIFT=`aws ec2 create-security-group --group-name ${SG_GROUP_NAME_REDSHIFT} --description "${SG_DESCRIPTION_REDSHIFT}" --vpc-id ${VPC_ID} --query GroupId | sed s/\"//g` && echo ${SG_ID_REDSHIFT}
```

```
sg-********
```

## 作成したSecurity Groupを確認

（アウトバウンドの通信のみ全て許可する設定のみ、がデフォルト）

```
aws ec2 describe-security-groups --group-ids ${SG_ID_REDSHIFT}
```

```
{
    "SecurityGroups": [
        {
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "UserIdGroupPairs": [],
                    "PrefixListIds": []
                }
            ],
            "Description": "JAWS-UG CLI at Co-Edo",
            "IpPermissions": [],
            "GroupName": "Redshift",
            "VpcId": "vpc-********",
            "OwnerId": "************",
            "GroupId": "sg-********"
        }
    ]
}
```

## インバウンドのルールを追加

Redshiftへの接続を許可します。

```
aws ec2 authorize-security-group-ingress --group-id ${SG_ID_REDSHIFT} --protocol 'tcp' --port 5439 --cidr ${PUBLIC_IP_ADDRESS}/32
```

```

```

## 追加されたルールを確認

```
aws ec2 describe-security-groups --group-ids ${SG_ID_REDSHIFT}
```

```
{
    "SecurityGroups": [
        {
            "IpPermissionsEgress": [
                {
                    "IpProtocol": "-1",
                    "IpRanges": [
                        {
                            "CidrIp": "0.0.0.0/0"
                        }
                    ],
                    "UserIdGroupPairs": [],
                    "PrefixListIds": []
                }
            ],
            "Description": "JAWS-UG CLI at Co-Edo",
            "IpPermissions": [
                {
                    "PrefixListIds": [],
                    "FromPort": 5439,
                    "IpRanges": [
                        {
                            "CidrIp": "**.**.**.**/32"
                        }
                    ],
                    "ToPort": 5439,
                    "IpProtocol": "tcp",
                    "UserIdGroupPairs": []
                }
            ],
            "GroupName": "Redshift",
            "VpcId": "vpc-********",
            "OwnerId": "************",
            "GroupId": "sg-********"
        }
    ]
}
```

以上