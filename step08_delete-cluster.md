





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

```

削除

```
aws redshift delete-cluster --cluster-identifier ${CLUSTER_NAME}
```

確認

```
aws redshift describe-clusters --cluster-identifier ${CLUSTER_NAME}
```

```

```

# 2. Subnet Groupの削除

確認

```
aws redshift describe-cluster-subnet-groups　--cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```

```

削除

```
aws redshift delete-cluster-subnet-group --cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

確認

```
aws redshift describe-cluster-subnet-groups　--cluster-subnet-group-name ${CLUSTER_SUBNET_GROUP_NAME}
```

```

```
