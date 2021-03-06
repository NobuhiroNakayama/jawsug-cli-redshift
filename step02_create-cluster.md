









## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX

    SUBNET_A_ID:${SUBNET_A_ID}
    SG_ID_REDSHIFT:${SG_ID_REDSHIFT}

ETX
```


# 1．Subnet Groupの作成

## パラメータの指定

```
CLUSTER_SUBNET_GROUP_NAME="redshift-subnet"
CLUSTER_SUBNET_GROUP_DESCRIPTION="Subnet Group for Redshift"
```

同名のSubnet Groupが存在しないことを確認

```
aws redshift describe-cluster-subnet-groups --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```
A client error (ClusterSubnetGroupNotFoundFault) occurred when calling the DescribeClusterSubnetGroups operation: Cluster Subnet group 'redshift-subnet' not found.
```

## 確認

```
cat << ETX

    CLUSTER_SUBNET_GROUP_NAME:${CLUSTER_SUBNET_GROUP_NAME}
    CLUSTER_SUBNET_GROUP_DESCRIPTION:${CLUSTER_SUBNET_GROUP_DESCRIPTION}
    SUBNET_A_ID:${SUBNET_A_ID}
    
ETX
```

```

    CLUSTER_SUBNET_GROUP_NAME:redshift-subnet
    CLUSTER_SUBNET_GROUP_DESCRIPTION:Subnet Group for Redshift
    SUBNET_A_ID:subnet-********
    
```

## 作成

```
aws redshift create-cluster-subnet-group --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}  --description "${CLUSTER_SUBNET_GROUP_DESCRIPTION}" --subnet-ids ${SUBNET_A_ID}
```

```
{
    "ClusterSubnetGroup": {
        "Subnets": [
            {
                "SubnetStatus": "Active",
                "SubnetIdentifier": "subnet-********",
                "SubnetAvailabilityZone": {
                    "Name": "ap-northeast-1a"
                }
            }
        ],
        "VpcId": "vpc-********",
        "Description": "Subnet Group for Redshift",
        "Tags": [],
        "SubnetGroupStatus": "Complete",
        "ClusterSubnetGroupName": "redshift-subnet"
    }
}
```

# 2. Security Groupの作成

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

## Redshiftのクラスターに設定するSecurity Groupを作成

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
（返値無し）
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


# 2．クラスタの作成

## クラスタ名

```
CLUSTER_NAME="mycluster"
```


## データベース名

```
DB_NAME="mydb"
```


## ポート番号

```
PORT="5439"
```


## マスターユーザの認証情報

```
MASTER_USER_NAME="awsuser"
MASTER_USER_PASSWORD="Pa55w0rd"
```


## Node type

```
NODE_TYPE="dc1.large"
```


## Cluster type

```
CLUSTER_TYPE="single-node"
```




## その他のパラメータについて

その他はデフォルトの設定を利用します。

デフォルト設定は、公式リファレンスを確認してください。

http://docs.aws.amazon.com/cli/latest/reference/redshift/create-cluster.html



## パラメータの確認

```
cat << ETX

    CLUSTER_NAME:${CLUSTER_NAME}
    DB_NAME:${DB_NAME}
    PORT:${PORT}
    MASTER_USER_NAME:${MASTER_USER_NAME}
    MASTER_USER_PASSWORD:${MASTER_USER_PASSWORD}
    NODE_TYPE:${NODE_TYPE}
    CLUSTER_TYPE:${CLUSTER_TYPE}

ETX
```

## クラスタの作成

```
aws redshift create-cluster \
--db-name ${DB_NAME} \
--cluster-identifier ${CLUSTER_NAME} \
--cluster-type ${CLUSTER_TYPE} \
--node-type ${NODE_TYPE} \
--master-username ${MASTER_USER_NAME} \
--master-user-password ${MASTER_USER_PASSWORD} \
--port ${PORT} \
--cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME} \
--vpc-security-group-ids ${SG_ID_REDSHIFT}
```

```
{
    "Cluster": {
        "IamRoles": [],
        "ClusterVersion": "1.0",
        "NumberOfNodes": 1,
        "VpcId": "vpc-********",
        "NodeType": "dc1.large",
        "PubliclyAccessible": true,
        "Tags": [],
        "MasterUsername": "awsuser",
        "ClusterParameterGroups": [
            {
                "ParameterGroupName": "default.redshift-1.0",
                "ParameterApplyStatus": "in-sync"
            }
        ],
        "Encrypted": false,
        "ClusterSecurityGroups": [],
        "AllowVersionUpgrade": true,
        "VpcSecurityGroups": [
            {
                "Status": "active",
                "VpcSecurityGroupId": "sg-********"
            }
        ],
        "ClusterSubnetGroupName": "redshift-sg",
        "AutomatedSnapshotRetentionPeriod": 1,
        "ClusterStatus": "creating",
        "ClusterIdentifier": "mycluster",
        "DBName": "mydb",
        "PreferredMaintenanceWindow": "sun:14:30-sun:15:00",
        "PendingModifiedValues": {
            "MasterUserPassword": "****"
        }
    }
}
```

## 確認

```
aws redshift describe-clusters --cluster-identifier ${CLUSTER_NAME}
```

```
{
    "Clusters": [
        {
            "PubliclyAccessible": true,
            "MasterUsername": "awsuser",
            "VpcSecurityGroups": [
                {
                    "Status": "active",
                    "VpcSecurityGroupId": "sg-********"
                }
            ],
            "NumberOfNodes": 1,
            "PendingModifiedValues": {
                "MasterUserPassword": "****"
            },
            "VpcId": "vpc-********",
            "ClusterVersion": "1.0",
            "Tags": [],
            "AutomatedSnapshotRetentionPeriod": 1,
            "ClusterParameterGroups": [
                {
                    "ParameterGroupName": "default.redshift-1.0",
                    "ParameterApplyStatus": "in-sync"
                }
            ],
            "DBName": "mydb",
            "PreferredMaintenanceWindow": "sun:14:30-sun:15:00",
            "IamRoles": [],
            "AllowVersionUpgrade": true,
            "ClusterSubnetGroupName": "redshift-sg",
            "ClusterSecurityGroups": [],
            "ClusterIdentifier": "mycluster",
            "ClusterNodes": [
                {
                    "NodeRole": "SHARED",
                    "PublicIPAddress": "**.**.**.**"
                }
            ],
            "AvailabilityZone": "ap-northeast-1a",
            "NodeType": "dc1.large",
            "Encrypted": false,
            "ClusterRevisionNumber": "1043",
            "ClusterStatus": "creating"
        }
    ]
}
```

以上