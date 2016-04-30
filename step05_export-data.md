



## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX



ETX
```




アンロードする

http://docs.aws.amazon.com/ja_jp/redshift/latest/dg/t_Unloading_tables.html

# S3バケットの作成

## バケット名の指定

バケット名は、グローバルでユニークである必要があります。適宜、バケット名を修正してください。

```
BUCKET_NAME="jawsug-cli-unloaded-data-2016-04-28"
```

## バケットの作成

```
aws s3 mb s3://${BUCKET_NAME}
```

```
make_bucket: s3://jawsug-cli-unloaded-data-2016-04-28/
```

## バケットの確認

```
aws s3 ls
```

```
2016-04-30 07:33:44 jawsug-cli-unloaded-data-2016-04-28
```

## バケット内のファイルの確認

```
aws s3 ls s3://${BUCKET_NAME}
```

```
（返値無し）
```


# アンロード

## パラメータの確認

```
cat << ETX

    REDSHIFT_ENDPOINT:${REDSHIFT_ENDPOINT}
    DB_NAME:${DB_NAME}
    PORT:${PORT}
    MASTER_USER_NAME:${MASTER_USER_NAME}

ETX
```

## コマンドの生成（アンロード）

```
UNLOAD_SAMPLE_DATA_FILE='unload_sample_data.txt'

cat << EOF > ${UNLOAD_SAMPLE_DATA_FILE}

unload ('select * from venue')   
to 's3://${BUCKET_NAME}/unload/' credentials 
'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}';

EOF
```

## コマンドの生成（アンロード結果の確認）

```
UNLOAD_SAMPLE_DATA_FILE='unload_sample_data.txt'

cat << EOF > ${UNLOAD_SAMPLE_DATA_FILE}

select query, substring(path,0,40) as path
from stl_unload_log
where query=2320
order by path;

EOF
```


## クラスターへのログイン

```
psql -h ${REDSHIFT_ENDPOINT} -U ${MASTER_USER_NAME} -d ${DB_NAME} -p ${PORT}
```

```
psql (9.2.15, server 8.0.2)
WARNING: psql version 9.2, server version 8.0.
         Some psql features might not work.
SSL connection (cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256)
Type "help" for help.

mydb=#
```


## アンロード

生成したコマンドを貼り付けます。


## アンロード結果の確認

```
select query, substring(path,0,100) as path
from stl_unload_log
order by path;
```

```
 query |                             path
-------+--------------------------------------------------------------
 10559 | s3://jawsug-cli-unloaded-data-2016-04-28/unload/0000_part_00
 10559 | s3://jawsug-cli-unloaded-data-2016-04-28/unload/0001_part_00
(2 rows)
```

## ログアウト

```
\q
```

# S3を参照

```
aws s3 ls s3://${BUCKET_NAME} --recursive
```

```
2016-04-30 08:05:49       3521 unload/0000_part_00
2016-04-30 08:05:49       4235 unload/0001_part_00
```

# オブジェクトの削除

```
aws s3 rm s3://${BUCKET_NAME} --recursive 
```

```
delete: s3://jawsug-cli-unloaded-data-2016-04-28/unload/
delete: s3://jawsug-cli-unloaded-data-2016-04-28/unload/0000_part_00
delete: s3://jawsug-cli-unloaded-data-2016-04-28/unload/0001_part_00
```


# バケットの削除

```
aws s3 rb s3://${BUCKET_NAME}
```

```
remove_bucket: s3://jawsug-cli-unloaded-data-2016-04-28/
```


# EC2インスタンスからログアウト

```
exit
```

以上
