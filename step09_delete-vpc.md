




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

```

削除

```
aws ec2 terminate-instances --instance-ids ${INSTANCE_ID}
```

```

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
aws iam get-instance-profile --instance-profile-name ${}
```

```

```

インスタンスプロファイルからIAMロールを削除

```
aws iam remove-role-from-instance-profile --instance-profile-name ${} --role-name ${}
```

インスタンスプロファイルの削除

```
aws iam delete-instance-profile --instance-profile-name ${}
```

```
（返値無し）
```


確認（Instance Profile）

```
aws iam get-instance-profile --instance-profile-name ${}
```

```

```


確認（IAM Role）

```
aws iam list-attached-role-policies --role-name ${}
```

```

```

デタッチ

```
aws iam detach-role-policy --role-name ${} --policy-arn ${}
aws iam detach-role-policy --role-name ${} --policy-arn ${}
aws iam detach-role-policy --role-name ${} --policy-arn ${}
```

```
（返値無し）
```


削除

```
aws iam delete-role --role-name ${}
```

```
（返値無し）
```


確認

```
aws iam list-attached-role-policies --role-name ${}
```

```

```

## 5. Key Pairおよび秘密鍵ファイルの削除

確認

```
aws ec2 describe-key-pairs --key-names ${KEY_PAIR_NAME}

ls -al ~/.ssh | grep ${KEY_MATERIAL_FILE}
```

```

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



```

## 6. VPCおよびVPCに関連するリソースの削除

Management ConsoleからVPCを削除すると、関連するリソースも一括で削除してくれます。
時間が無い方は、Management Consoleから削除しましょう。

### セキュリティグループの削除

確認

```
aws ec2 describe-security-groups --group-ids ${}
aws ec2 describe-security-groups --group-ids ${}
```

```

```

削除

```
aws ec2 delete-security-group --group-id ${}
aws ec2 delete-security-group --group-id ${}
```

```
（返値無し）
```

確認

```
aws ec2 describe-security-groups --group-ids ${}
aws ec2 describe-security-groups --group-ids ${}
```

```

```

### Subnetの削除

確認

```
aws ec2 describe-subnets --subnet-ids ${SUBNET_A_ID}
```

```

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
