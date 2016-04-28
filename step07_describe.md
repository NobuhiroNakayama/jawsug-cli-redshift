



## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX



ETX
```


# 1. 各種設定の確認

## クラスターパラメータグループの確認

パラメータグループとは、

```
aws redshift describe-cluster-parameter-groups
```

```
{
    "ParameterGroups": [
        {
            "ParameterGroupFamily": "redshift-1.0",
            "Tags": [],
            "ParameterGroupName": "default.redshift-1.0",
            "Description": "Default parameter group for redshift-1.0"
        }
    ]
}
```

## クラスターパラメータグループファミリーの特定

```
CLUSTER_PARAMETER_GROUP_FAMILY=`aws redshift describe-cluster-parameter-groups --query ParameterGroups[0].ParameterGroupFamily | sed s/\"//g` && echo ${CLUSTER_PARAMETER_GROUP_FAMILY}
```

```
redshift-1.0
```

## デフォルトクラスターパラメータの確認

```
aws redshift describe-default-cluster-parameters --parameter-group-family ${CLUSTER_PARAMETER_GROUP_FAMILY}
```

```
{
    "DefaultClusterParameters": {
        "Parameters": [
            {
                "Description": "Sets the display format for date and time values.",
                "DataType": "string",
                "IsModifiable": true,
                "Source": "engine-default",
                "ParameterValue": "ISO, MDY",
                "ParameterName": "datestyle",
                "ApplyType": "static"
            },
            {
                "Description": "parameter for audit logging purpose",
                "DataType": "boolean",
                "IsModifiable": true,
                "AllowedValues": "true,false",
                "Source": "engine-default",
                "ParameterValue": "false",
                "ParameterName": "enable_user_activity_logging",
                "ApplyType": "static"
            },
            {
                "Description": "Sets the number of digits displayed for floating-point values",
                "DataType": "integer",
                "IsModifiable": true,
                "AllowedValues": "-15-2",
                "Source": "engine-default",
                "ParameterValue": "0",
                "ParameterName": "extra_float_digits",
                "ApplyType": "static"
            },
            {
                "Description": "Sets the max cursor result set size",
                "DataType": "integer",
                "IsModifiable": true,
                "AllowedValues": "0-14400000",
                "Source": "engine-default",
                "ParameterValue": "default",
                "ParameterName": "max_cursor_result_set_size",
                "ApplyType": "static"
            },
            {
                "Description": "This parameter applies a user-defined label to a group of queries that are run during the same session..",
                "DataType": "string",
                "IsModifiable": true,
                "Source": "engine-default",
                "ParameterValue": "default",
                "ParameterName": "query_group",
                "ApplyType": "static"
            },
            {
                "Description": "require ssl for all databaseconnections",
                "DataType": "boolean",
                "IsModifiable": true,
                "AllowedValues": "true,false",
                "Source": "engine-default",
                "ParameterValue": "false",
                "ParameterName": "require_ssl",
                "ApplyType": "static"
            },
            {
                "Description": "Sets the schema search order for names that are not schema-qualified.",
                "DataType": "string",
                "IsModifiable": true,
                "Source": "engine-default",
                "ParameterValue": "$user, public",
                "ParameterName": "search_path",
                "ApplyType": "static"
            },
            {
                "Description": "Aborts any statement that takes over the specified number of milliseconds.",
                "DataType": "integer",
                "IsModifiable": true,
                "AllowedValues": "0,100-2147483647",
                "Source": "engine-default",
                "ParameterValue": "0",
                "ParameterName": "statement_timeout",
                "ApplyType": "static"
            },
            {
                "Description": "wlm json configuration",
                "DataType": "string",
                "IsModifiable": true,
                "Source": "engine-default",
                "ParameterValue": "[{\"query_concurrency\":5}]",
                "ParameterName": "wlm_json_configuration",
                "ApplyType": "static"
            }
        ]
    }
}
```

## デフォルトクラスターパラメータグループの特定

```
CLUSTER_PARAMETER_GROUP_NAME=`aws redshift describe-cluster-parameter-groups --query ParameterGroups[0].ParameterGroupName | sed s/\"//g` && echo ${CLUSTER_PARAMETER_GROUP_NAME}
```

```
default.redshift-1.0
```


## クラスターパラメータの確認

```
aws redshift describe-cluster-parameters --parameter-group-name ${CLUSTER_PARAMETER_GROUP_NAME}

```

```
{
    "Parameters": [
        {
            "Description": "Sets the display format for date and time values.",
            "DataType": "string",
            "IsModifiable": true,
            "Source": "engine-default",
            "ParameterValue": "ISO, MDY",
            "ParameterName": "datestyle",
            "ApplyType": "static"
        },
        {
            "Description": "parameter for audit logging purpose",
            "DataType": "boolean",
            "IsModifiable": true,
            "AllowedValues": "true,false",
            "Source": "engine-default",
            "ParameterValue": "false",
            "ParameterName": "enable_user_activity_logging",
            "ApplyType": "static"
        },
        {
            "Description": "Sets the number of digits displayed for floating-point values",
            "DataType": "integer",
            "IsModifiable": true,
            "AllowedValues": "-15-2",
            "Source": "engine-default",
            "ParameterValue": "0",
            "ParameterName": "extra_float_digits",
            "ApplyType": "static"
        },
        {
            "Description": "Sets the max cursor result set size",
            "DataType": "integer",
            "IsModifiable": true,
            "AllowedValues": "0-14400000",
            "Source": "engine-default",
            "ParameterValue": "default",
            "ParameterName": "max_cursor_result_set_size",
            "ApplyType": "static"
        },
        {
            "Description": "This parameter applies a user-defined label to a group of queries that are run during the same session..",
            "DataType": "string",
            "IsModifiable": true,
            "Source": "engine-default",
            "ParameterValue": "default",
            "ParameterName": "query_group",
            "ApplyType": "static"
        },
        {
            "Description": "require ssl for all databaseconnections",
            "DataType": "boolean",
            "IsModifiable": true,
            "AllowedValues": "true,false",
            "Source": "engine-default",
            "ParameterValue": "false",
            "ParameterName": "require_ssl",
            "ApplyType": "static"
        },
        {
            "Description": "Sets the schema search order for names that are not schema-qualified.",
            "DataType": "string",
            "IsModifiable": true,
            "Source": "engine-default",
            "ParameterValue": "$user, public",
            "ParameterName": "search_path",
            "ApplyType": "static"
        },
        {
            "Description": "Aborts any statement that takes over the specified number of milliseconds.",
            "DataType": "integer",
            "IsModifiable": true,
            "AllowedValues": "0,100-2147483647",
            "Source": "engine-default",
            "ParameterValue": "0",
            "ParameterName": "statement_timeout",
            "ApplyType": "static"
        },
        {
            "Description": "wlm json configuration",
            "DataType": "string",
            "IsModifiable": true,
            "Source": "engine-default",
            "ParameterValue": "[{\"query_concurrency\":5}]",
            "ParameterName": "wlm_json_configuration",
            "ApplyType": "static"
        }
    ]
}
```

## クラスターバージョンの確認

```
aws redshift describe-cluster-versions
```

```
{
    "ClusterVersions": [
        {
            "ClusterVersion": "1.0",
            "Description": "Release DB 1.0.647",
            "ClusterParameterGroupFamily": "redshift-1.0"
        }
    ]
}
```

## クラスタースナップショットの確認

Redshiftのクラスターは、自動でスナップショットが取得されます。

```
aws redshift describe-cluster-snapshots
```

```
{
    "Snapshots": [
        {
            "EstimatedSecondsToCompletion": -1,
            "OwnerAccount": "************",
            "CurrentBackupRateInMegaBytesPerSecond": 0.0,
            "ActualIncrementalBackupSizeInMegaBytes": 12.0,
            "NumberOfNodes": 1,
            "Status": "available",
            "VpcId": "vpc-********",
            "ClusterVersion": "1.0",
            "Tags": [],
            "MasterUsername": "awsuser",
            "TotalBackupSizeInMegaBytes": 12.0,
            "DBName": "mydb",
            "BackupProgressInMegaBytes": 12.0,
            "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
            "RestorableNodeTypes": [
                "dc1.large"
            ],
            "EncryptedWithHSM": false,
            "ClusterIdentifier": "mycluster",
            "SnapshotCreateTime": "2016-04-28T10:50:56.242Z",
            "AvailabilityZone": "ap-northeast-1a",
            "NodeType": "dc1.large",
            "Encrypted": false,
            "ElapsedTimeInSeconds": 1,
            "SnapshotType": "automated",
            "Port": 5439,
            "SnapshotIdentifier": "rs:mycluster-2016-04-28-10-50-56"
        }
    ]
}
```

## ？？？

（後で調査）

```
aws redshift describe-snapshot-copy-grants
```

```
{
    "SnapshotCopyGrants": []
}
```

## ？？？

（後で調査）

```
aws redshift describe-table-restore-status --cluster-identifier ${CLUSTER_NAME}
```

```
{
    "TableRestoreStatusDetails": []
}
```

## 



```
aws redshift describe-events
```

```
{
    "Events": []
}
```



```
aws 






describe-event-categories
describe-event-subscriptions
describe-events




describe-logging-status

describe-orderable-cluster-options

describe-resize

describe-tags

```


