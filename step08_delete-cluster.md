





## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX

    CLUSTER_NAME:${CLUSTER_NAME}
    CLUSTER_SUBNET_GROUP_NAME:${CLUSTER_SUBNET_GROUP_NAME}

ETX
```

# 1. クラスタの削除

確認

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
            "ClusterPublicKey": "ssh-rsa ************************ Amazon-Redshift\n",
            "NumberOfNodes": 1,
            "PendingModifiedValues": {},
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
            "PreferredMaintenanceWindow": "fri:17:30-fri:18:00",
            "Endpoint": {
                "Port": 5439,
                "Address": "mycluster.************.ap-northeast-1.redshift.amazonaws.com"
            },
            "AllowVersionUpgrade": true,
            "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
            "ClusterSubnetGroupName": "redshift-subnet",
            "ClusterSecurityGroups": [],
            "ClusterIdentifier": "mycluster",
            "ClusterNodes": [
                {
                    "NodeRole": "SHARED",
                    "PrivateIPAddress": "10.0.0.164",
                    "PublicIPAddress": "**.**.**.**"
                }
            ],
            "AvailabilityZone": "ap-northeast-1a",
            "NodeType": "dc1.large",
            "Encrypted": false,
            "ClusterRevisionNumber": "1044",
            "ClusterStatus": "available"
        }
    ]
}
```

削除

（10分くらいかかります）

```
aws redshift delete-cluster --cluster-identifier ${CLUSTER_NAME} --skip-final-cluster-snapshot
```

```
{
    "Cluster": {
        "PubliclyAccessible": true,
        "MasterUsername": "awsuser",
        "VpcSecurityGroups": [
            {
                "Status": "active",
                "VpcSecurityGroupId": "sg-********"
            }
        ],
        "NumberOfNodes": 1,
        "PendingModifiedValues": {},
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
        "PreferredMaintenanceWindow": "fri:17:30-fri:18:00",
        "Endpoint": {
            "Port": 5439,
            "Address": "mycluster.************.ap-northeast-1.redshift.amazonaws.com"
        },
        "AllowVersionUpgrade": true,
        "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
        "ClusterSubnetGroupName": "redshift-subnet",
        "ClusterSecurityGroups": [],
        "ClusterIdentifier": "mycluster",
        "AvailabilityZone": "ap-northeast-1a",
        "NodeType": "dc1.large",
        "Encrypted": false,
        "ClusterStatus": "deleting"
    }
}
```

確認

```
aws redshift describe-clusters --cluster-identifier ${CLUSTER_NAME}
```

```
A client error (ClusterNotFound) occurred when calling the DescribeClusters operation: Cluster mycluster not found.
```

# 2. Subnet Groupの削除

確認

```
aws redshift describe-cluster-subnet-groups --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```
{
    "ClusterSubnetGroups": [
        {
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
    ]
}
```

削除

```
aws redshift delete-cluster-subnet-group --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```
（返値無し）
```

確認

```
aws redshift describe-cluster-subnet-groups --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```
A client error (ClusterSubnetGroupNotFoundFault) occurred when calling the DescribeClusterSubnetGroups operation: Cluster Subnet group 'redshift-subnet' not found.
```

以上
