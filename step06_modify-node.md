



## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX

   CLUSTER_NAME: ${CLUSTER_NAME}


ETX
```



# 1. スナップショットの作成、削除

## スナップショット名の指定

```
SNAPSHOT_NAME=mysnapshot
```


## パラメータの確認

```
cat << ETX

   CLUSTER_NAME: ${CLUSTER_NAME}
   SNAPSHOT_NAME: ${SNAPSHOT_NAME}

ETX
```

```

   CLUSTER_NAME: mycluster
   SNAPSHOT_NAME: mysnapshot

```


## スナップショットの作成

```
aws redshift create-cluster-snapshot --snapshot-identifier ${SNAPSHOT_NAME} --cluster-identifier ${CLUSTER_NAME}
```

```
{
    "Snapshot": {
        "EstimatedSecondsToCompletion": -1,
        "OwnerAccount": "************",
        "CurrentBackupRateInMegaBytesPerSecond": 0.0,
        "ActualIncrementalBackupSizeInMegaBytes": -1.0,
        "NumberOfNodes": 1,
        "Status": "creating",
        "VpcId": "vpc-********",
        "ClusterVersion": "1.0",
        "Tags": [],
        "MasterUsername": "awsuser",
        "TotalBackupSizeInMegaBytes": -1.0,
        "DBName": "mydb",
        "BackupProgressInMegaBytes": 0.0,
        "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
        "EncryptedWithHSM": false,
        "ClusterIdentifier": "mycluster",
        "SnapshotCreateTime": "2016-04-28T13:21:08.756Z",
        "AvailabilityZone": "ap-northeast-1a",
        "NodeType": "dc1.large",
        "Encrypted": false,
        "ElapsedTimeInSeconds": 0,
        "SnapshotType": "manual",
        "Port": 5439,
        "SnapshotIdentifier": "mysnapshot"
    }
}
```

## スナップショットの確認

```
aws redshift describe-cluster-snapshots --query Snapshots[?SnapshotType==\'manual\']
```

```
[
    {
        "EstimatedSecondsToCompletion": 0,
        "OwnerAccount": "************",
        "CurrentBackupRateInMegaBytesPerSecond": 53.8349,
        "ActualIncrementalBackupSizeInMegaBytes": 332.0,
        "NumberOfNodes": 1,
        "Status": "available",
        "VpcId": "vpc-********",
        "ClusterVersion": "1.0",
        "Tags": [],
        "MasterUsername": "awsuser",
        "TotalBackupSizeInMegaBytes": 339.0,
        "DBName": "mydb",
        "BackupProgressInMegaBytes": 332.0,
        "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
        "RestorableNodeTypes": [
            "dc1.large"
        ],
        "EncryptedWithHSM": false,
        "ClusterIdentifier": "mycluster",
        "SnapshotCreateTime": "2016-04-28T13:21:32.303Z",
        "AvailabilityZone": "ap-northeast-1a",
        "NodeType": "dc1.large",
        "Encrypted": false,
        "ElapsedTimeInSeconds": 6,
        "SnapshotType": "manual",
        "Port": 5439,
        "SnapshotIdentifier": "mysnapshot"
    }
]
```

## スナップショットの削除

```
aws redshift delete-cluster-snapshot --snapshot-identifier ${SNAPSHOT_NAME}
```

```
{
    "Snapshot": {
        "EstimatedSecondsToCompletion": 0,
        "OwnerAccount": "************",
        "CurrentBackupRateInMegaBytesPerSecond": 53.8349,
        "ActualIncrementalBackupSizeInMegaBytes": 332.0,
        "NumberOfNodes": 1,
        "Status": "deleted",
        "VpcId": "vpc-********",
        "ClusterVersion": "1.0",
        "Tags": [],
        "MasterUsername": "awsuser",
        "TotalBackupSizeInMegaBytes": 339.0,
        "DBName": "mydb",
        "BackupProgressInMegaBytes": 332.0,
        "ClusterCreateTime": "2016-04-28T10:47:23.091Z",
        "EncryptedWithHSM": false,
        "ClusterIdentifier": "mycluster",
        "SnapshotCreateTime": "2016-04-28T13:21:32.303Z",
        "AvailabilityZone": "ap-northeast-1a",
        "NodeType": "dc1.large",
        "Encrypted": false,
        "ElapsedTimeInSeconds": 6,
        "SnapshotType": "manual",
        "Port": 5439,
        "SnapshotIdentifier": "mysnapshot"
    }
}
```

## 確認

```
aws redshift describe-cluster-snapshots --query Snapshots[?SnapshotType==\'manual\']
```

```
[]
```
