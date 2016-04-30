




## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX



ETX
```






# 2. EC2インスタンスの削除

## EC2インスタンスを削除

パラメータを確認

```
cat << ETX

    INSTANCE_ID: ${INSTANCE_ID}

ETX
```

```
i-********
```

削除

```
aws ec2 terminate-instances --instance-ids ${INSTANCE_ID}
```

```
{
    "TerminatingInstances": [
        {
            "InstanceId": "i-7b972de4",
            "CurrentState": {
                "Code": 32,
                "Name": "shutting-down"
            },
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}
```

確認

Stateがshutting-downやterminatedであればOK

```
aws ec2 describe-instances --instance-ids ${INSTANCE_ID}
```

```
{
    "Reservations": [
        {
            "OwnerId": "************",
            "ReservationId": "r-********",
            "Groups": [],
            "Instances": [
                {
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "PublicDnsName": "",
                    "Platform": "windows",
                    "State": {
                        "Code": 48,
                        "Name": "terminated"
                    },
                    "EbsOptimized": false,
                    "LaunchTime": "2015-10-17T13:28:19.000Z",
                    "ProductCodes": [],
                    "StateTransitionReason": "User initiated (2015-10-17 16:00:38 GMT)",
                    "InstanceId": "i-********",
                    "ImageId": "ami-4623a846",
                    "PrivateDnsName": "",
                    "KeyName": "UserManagementServer",
                    "SecurityGroups": [],
                    "ClientToken": "",
                    "InstanceType": "t2.medium",
                    "NetworkInterfaces": [],
                    "Placement": {
                        "Tenancy": "default",
                        "GroupName": "",
                        "AvailabilityZone": "us-west-2a"
                    },
                    "Hypervisor": "xen",
                    "BlockDeviceMappings": [],
                    "Architecture": "x86_64",
                    "StateReason": {
                        "Message": "Client.UserInitiatedShutdown: User initiated shutdown",
                        "Code": "Client.UserInitiatedShutdown"
                    },
                    "RootDeviceName": "/dev/sda1",
                    "VirtualizationType": "hvm",
                    "RootDeviceType": "ebs",
                    "AmiLaunchIndex": 0
                }
            ]
        }
    ]
}
```

## 4. インスタンスプロファイルおよびIAMロールの削除

確認（インスタンスプロファイル）

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
                "CreateDate": "2016-04-28T10:35:37Z",
                "RoleName": "redshift-role",
                "Path": "/",
                "Arn": "arn:aws:iam::************:role/redshift-role"
            }
        ],
        "CreateDate": "2016-04-28T10:36:30Z",
        "InstanceProfileName": "redshift-role",
        "Path": "/",
        "Arn": "arn:aws:iam::************:instance-profile/redshift-role"
    }
}
```

インスタンスプロファイルからIAMロールを削除

```
aws iam remove-role-from-instance-profile --instance-profile-name ${ROLE_NAME} --role-name ${ROLE_NAME}
```

```
（返値無し）
```

インスタンスプロファイルの削除

```
aws iam delete-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
（返値無し）
```


確認（Instance Profile）

```
aws iam get-instance-profile --instance-profile-name ${ROLE_NAME}
```

```
A client error (NoSuchEntity) occurred when calling the GetInstanceProfile operation: Instance Profile redshift-role cannot be found.
```


確認（IAM Role）

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

デタッチ

```
aws iam detach-role-policy --role-name ${ROLE_NAME} --policy-arn ${IAM_POLICY_ARN}
aws iam detach-role-policy --role-name ${ROLE_NAME} --policy-arn ${RED_POLICY_ARN}
aws iam detach-role-policy --role-name ${ROLE_NAME} --policy-arn ${S3_POLICY_ARN}
```

```
（返値無し）
```


削除

```
aws iam delete-role --role-name ${ROLE_NAME}
```

```
（返値無し）
```


確認

```
aws iam list-attached-role-policies --role-name ${ROLE_NAME}
```

```
A client error (NoSuchEntity) occurred when calling the ListAttachedRolePolicies operation: Role redshift-role does not exist.
```

## 5. Key Pairおよび秘密鍵ファイルの削除

確認

```
aws ec2 describe-key-pairs --key-names ${KEY_PAIR_NAME}

ls -al ~/.ssh | grep ${KEY_MATERIAL_FILE}
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

-rw------- 1 ec2-user ec2-user 1671 Apr 28 10:30 redshift.pem
```

削除

```
aws ec2 delete-key-pair --key-name ${KEY_PAIR_NAME}

rm ~/.ssh/${KEY_MATERIAL_FILE}
```

```
（返値無し）
```

確認

```
aws ec2 describe-key-pairs --key-names ${KEY_PAIR_NAME}

ls -al ~/.ssh | grep ${KEY_MATERIAL_FILE}
```

```
A client error (InvalidKeyPair.NotFound) occurred when calling the DescribeKeyPairs operation: The key pair 'Redshift' does not exist
```

## 6. VPCおよびVPCに関連するリソースの削除

Management ConsoleからVPCを削除すると、関連するリソースも一括で削除してくれます。
時間が無い方は、Management Consoleから削除しましょう。

### セキュリティグループの削除

確認

```
aws ec2 describe-security-groups --group-ids ${SG_ID_SSH}
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

削除

```
aws ec2 delete-security-group --group-id ${SG_ID_REDSHIFT}
aws ec2 delete-security-group --group-id ${SG_ID_SSH}
```

```
（返値無し）
```

確認

```
aws ec2 describe-security-groups --group-ids ${SG_ID_REDSHIFT}
aws ec2 describe-security-groups --group-ids ${SG_ID_SSH}
```

```
A client error (InvalidGroup.NotFound) occurred when calling the DescribeSecurityGroups operation: The security group 'sg-********' does not exist

A client error (InvalidGroup.NotFound) occurred when calling the DescribeSecurityGroups operation: The security group 'sg-********' does not exist
```

### Subnetの削除

確認

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

削除

```
aws ec2 delete-subnet --subnet-id ${SUBNET_A_ID}
```

```
（返値無し）
```

確認

```
aws ec2 describe-subnets --subnet-ids ${SUBNET_A_ID}
```

```
A client error (InvalidSubnetID.NotFound) occurred when calling the DescribeSubnets operation: The subnet ID 'subnet-********' does not exist
```

### Internet Gatewayのデタッチ、削除

確認

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

デタッチ

```
aws ec2 detach-internet-gateway --internet-gateway-id ${IGW_ID} --vpc-id ${VPC_ID}
```

```
（返値無し）
```

確認

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

削除

```
aws ec2 delete-internet-gateway --internet-gateway-id ${IGW_ID}
```

```
（返値無し）
```

確認

```
aws ec2 describe-internet-gateways --internet-gateway-ids ${IGW_ID}
```

```
A client error (InvalidInternetGatewayID.NotFound) occurred when calling the DescribeInternetGateways operation: The internetGateway ID 'igw-********' does not exist
```

### VPCの削除

確認

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

削除

```
aws ec2 delete-vpc --vpc-id ${VPC_ID}
```

```
（返値無し）
```

確認

```
aws ec2 describe-vpcs --vpc-ids ${VPC_ID}
```

```
A client error (InvalidVpcID.NotFound) occurred when calling the DescribeVpcs operation: The vpc ID 'vpc-********' does not exist
```

以上です。お疲れ様でした。
