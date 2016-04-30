



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

## イベント関連のコマンドを確認

イベントとは、

SNSを利用し、条件を満たすイベントが発生した際に通知することが可能


```
aws redshift describe-events
```

```
{
    "Events": []
}
```

```
aws redshift describe-event-categories
```

```
{
    "EventCategoriesMapList": [
        {
            "SourceType": "cluster",
            "Events": [
                {
                    "EventId": "REDSHIFT-EVENT-2000",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> created at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2001",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> deleted at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3000",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> rebooted at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3001",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> node replaced at <time in UTC>. Cluster operating normally."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4000",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Master credentials updated for cluster <cluster name> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1000",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Parameter group <parameter group name> changes were associated with cluster <cluster name> at <time in UTC>. Changes will be applied on reboot."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2002",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "VPC security groups for cluster <cluster name> updated at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2003",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Maintenance started on cluster <cluster name> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2004",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Maintenance on cluster <cluster name> completed at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2006",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> resize started at <time in UTC>. Cluster is in read-only mode."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2007",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "A resize request for cluster <cluster name> has been acknowledged."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3002",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> resize completed at <time in UTC>. Cluster is available for both reads and writes."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3500",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cluster <cluster name> resize failed at <time in UTC>. Operation will be retried within a few minutes."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1001",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> parameter group changed to <parameter group name> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1500",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "VPC <VPC name> does not exist. Configuration changes for cluster <cluster name> were not applied."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1501",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Subnets in cluster subnet group <subnet name> for Amazon VPC <VPC name> do not exist or are invalid. Cluster <cluster name> could not be created."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1502",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Subnets in cluster subnet group <subnet name> have no available IP addresses. Cluster <cluster name> could not be created."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1503",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "VPC <VPC name> has no Internet gateway. Configuration changes for cluster <cluster name> were not applied."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2008",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster restore <cluster name> from snapshot <snapshot name> started at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3003",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster <cluster name> was restored from cluster snapshot <snapshot name> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3501",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cluster restore <cluster name> from cluster snapshot <snapshot name> failed at <time in UTC>. Retry from the Amazon Redshift console."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4500",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "A VPC security group is invalid. Configuration changes for cluster <cluster name> were not applied."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4501",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "An EC2 security group <security group name> specified in cluster security group <security group name> was not found. Authorization cannot be completed."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4001",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Security group of cluster <cluster name> modified at <time in UTC>. Changes will be automatically applied to all associated clusters."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1504",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Unable to connect to HSM for cluster <cluster name>.  Please consult the documentation for troubleshooting details."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1505",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Unable to register HSM for cluster <cluster name>. Please provide a different configuration."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3007",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Your Snapshot <snapshot name> was successfully copied from region <source region> to <destination region> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3504",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "S3 bucket <S3 bucket name> is no longer valid for logging for cluster <cluster name>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3505",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "The S3 Bucket <S3 bucket name> does not have the necessary IAM policies for cluster <cluster name>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3506",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "S3 bucket <S3 bucket name> does not exist. Logging cannot be continued for cluster <cluster name>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3507",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cluster <cluster name> cannot be created.  EIP <ip address> is currently in use."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3508",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cluster <cluster name> cannot be created. EIP <ip address> could not be found."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3509",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cross Region Snapshot Copy is not enabled for cluster <cluster name>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2013",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster renamed to <new cluster identifier> at <time in UTC>."
                }
            ]
        },
        {
            "SourceType": "cluster-security-group",
            "Events": [
                {
                    "EventId": "REDSHIFT-EVENT-4002",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster security group <security group name> created at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4003",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster security group <security group name> deleted at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-4004",
                    "EventCategories": [
                        "security"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster security group <security group name> updated at <time in UTC>. Changes will be automatically applied to associated clusters."
                }
            ]
        },
        {
            "SourceType": "cluster-parameter-group",
            "Events": [
                {
                    "EventId": "REDSHIFT-EVENT-1002",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Parameter <parameter name> in Parameter Group <parameter group name> was updated to <new value> at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1003",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster parameter group <parameter group name> created at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1004",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster parameter group <parameter group name> deleted at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-1005",
                    "EventCategories": [
                        "configuration"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster parameter group <parameter group name> was updated at <time in UTC>. Changes will be applied to associated clusters on reboot."
                }
            ]
        },
        {
            "SourceType": "cluster-snapshot",
            "Events": [
                {
                    "EventId": "REDSHIFT-EVENT-2009",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster snapshot <snapshot name> for cluster <cluster name> started at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3004",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster snapshot <snapshot name> for cluster <cluster name> completed successfully at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3503",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Cluster snapshot <snapshot name> for cluster <cluster name> failed at time <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2010",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster snapshot <snapshot name>' for cluster <cluster name> cancelled at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2011",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Cluster snapshot <snapshot name> for cluster <cluster name> was deleted at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-2012",
                    "EventCategories": [
                        "management"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Final cluster snapshot <snapshot name> for cluster <cluster name> started at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3005",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Final cluster snapshot <snapshot name> for cluster <cluster name> succeeded at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3502",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "ERROR",
                    "EventDescription": "Final cluster snapshot <snapshot name> for cluster <cluster name> failed at <time in UTC>."
                },
                {
                    "EventId": "REDSHIFT-EVENT-3006",
                    "EventCategories": [
                        "monitoring"
                    ],
                    "Severity": "INFO",
                    "EventDescription": "Final cluster snapshot <snapshot name> for cluster <cluster name> cancelled at <time in UTC>."
                }
            ]
        }
    ]
}
```

```
aws redshift describe-event-subscriptions
```

```
{
    "EventSubscriptionsList": []
}
```

## ログの取得設定の確認

S3バケットにログを出力可能


```
aws redshift describe-logging-status --cluster-identifier mycluster
```

```
{
    "LoggingEnabled": false
}
```


## ？？？

（調査）

```
aws redshift describe-orderable-cluster-options
```

```
{
    "OrderableClusterOptions": [
        {
            "NodeType": "ds2.8xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "ds2.xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "ds2.xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "single-node"
        },
        {
            "NodeType": "ds1.8xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "ds1.xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "ds1.xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "single-node"
        },
        {
            "NodeType": "dc1.8xlarge",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "dc1.large",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "multi-node"
        },
        {
            "NodeType": "dc1.large",
            "AvailabilityZones": [
                {
                    "Name": "ap-northeast-1a"
                },
                {
                    "Name": "ap-northeast-1c"
                }
            ],
            "ClusterVersion": "1.0",
            "ClusterType": "single-node"
        }
    ]
}
```


## タグ付け状況の確認

```
aws redshift describe-tags
```

```
{
    "TaggedResources": []
}
```

以上です。
