# 前提条件

## バージョン確認

このハンズオンは以下のバージョンで動作確認を行いました。

```bash:コマンド
aws --version
```

```text:結果

```

## 必要な権限

作業にあたっては、以下の権限を有したIAMユーザもしくはIAMロールを利用してください。

- EC2に対するフルコントロール権限
- RedShiftに関するフルコントロール権限
- IAMに関するフルコントロール権限


# 0. 準備

## リージョンを指定

```bash:コマンド
export AWS_DEFAULT_REGION='ap-northeast-1'
```

## 資格情報を確認

```bash:コマンド
aws configure list
```

```text:結果

```

## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX



ETX
```


# 1．Subnet Groupの作成

## パラメータの指定

```
CLUSTER_SUBNET_GROUP_NAME="redshift-subnet"
CLUSTER_SUBNET_GROUP_DESCRIPTION="Subnet Group for Redshift"
```


## 確認

```
cat << ETX

    :${CLUSTER_SUBNET_GROUP_NAME}
    :${CLUSTER_SUBNET_GROUP_DESCRIPTION}
    :${SUBNET_A_ID}
ETX
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
[--cluster-version <value>]
[--allow-version-upgrade | --no-allow-version-upgrade]
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
aws redshift describe-clusters
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
