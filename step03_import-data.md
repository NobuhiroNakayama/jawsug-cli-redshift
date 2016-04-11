





## パラメータの確認

前の章までに設定したパラメータのうち、この章で利用するパラメータは以下の通りです。

```
cat << ETX

    KEY_MATERIAL_FILE:${KEY_MATERIAL_FILE}
    PUBLIC_IP_ADDRESS:${PUBLIC_IP_ADDRESS}

ETX
```


# 1．クライアントへの接続

## SSHで接続

```
ssh -l ec2-user -i ~/.ssh/${KEY_MATERIAL_FILE} ${PUBLIC_IP_ADDRESS}
```


# 2．クライアントツールのインストール

psqlを利用して接続します。

参考情報
http://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/connecting-from-psql.html

PostgreSQLのインストール

```
sudo yum install postgresql-server -y
sudo yum install jq -y
```

リージョンを指定

```
export AWS_DEFAULT_REGION='ap-northeast-1'
```

資格情報を確認

```
aws configure list
```

```
Name                    Value             Type    Location
----                    -----             ----    --------
profile                <not set>             None    None
access_key     ****************RDPA         iam-role
secret_key     ****************9GA8         iam-role
region           ap-northeast-1              env    AWS_DEFAULT_REGION
```

エンドポイントの確認

```
REDSHIFT_ENDPOINT=`aws redshift describe-clusters --query Clusters[?ClusterIdentifier==\'mycluster\'] | jq .[].Endpoint | jq -r .Address` && echo ${REDSHIFT_ENDPOINT}
```

ユーザ名、DB名、ポート番号の確認

```
DB_NAME="mydb"
PORT="5439"
MASTER_USER_NAME="awsuser"
MASTER_USER_PASSWORD="Pa55w0rd"
```

パラメータの確認

```
cat << ETX

    REDSHIFT_ENDPOINT:${REDSHIFT_ENDPOINT}
    DB_NAME:${DB_NAME}
    PORT:${PORT}
    MASTER_USER_NAME:${MASTER_USER_NAME}
    MASTER_USER_PASSWORD:${MASTER_USER_PASSWORD}

ETX
```

接続確認
（パスワード入力を要求されます）

```
psql -h ${REDSHIFT_ENDPOINT} -U ${MASTER_USER_NAME} -d ${DB_NAME} -p ${PORT}
```

バージョン確認

```
select version();
```

```
                                                         version

------------------------------------------------------------------------------------------------
--------------------------
 PostgreSQL 8.0.2 on i686-pc-linux-gnu, compiled by GCC gcc (GCC) 3.4.2 20041017 (Red Hat 3.4.2-
6.fc3), Redshift 1.0.1043
(1 row)
```

切断

```
\q
```

# 3.アクセスキーの取得

ユーザ名の確認

```
IAM_USER_NAME="redshift_user"
```

ユーザの有無の確認

```
aws iam get-user --user-name ${IAM_USER_NAME}
```

```
A client error (NoSuchEntity) occurred when calling the GetUser operation: The user with name redshift_user cannot be found.
```

IAMユーザの作成

```
aws iam create-user --user-name ${IAM_USER_NAME}
```

```
{
    "User": {
        "UserName": "redshift_user",
        "Path": "/",
        "CreateDate": "2016-04-09T13:11:34.607Z",
        "UserId": "A********************",
        "Arn": "arn:aws:iam::************:user/redshift_user"
    }
}
```

ポリシーのアタッチ

```
aws iam attach-user-policy --user-name ${IAM_USER_NAME} --policy-arn "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
```

```

```

確認

```
aws iam list-attached-user-policies --user-name ${IAM_USER_NAME}
```

```
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonS3ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
        }
    ]
}
```

アクセスキーの作成

```
RESPONSE_FILE="access_key.json"
aws iam create-access-key --user-name ${IAM_USER_NAME} > ${RESPONSE_FILE} && cat ${RESPONSE_FILE}
```

アクセスキーの取得

```
ACCESS_KEY_ID=`jq -r .AccessKey.AccessKeyId ${RESPONSE_FILE}`
```

アクセスシークレットキーの取得

```
SECRET_ACCESS_KEY=`jq -r .AccessKey.SecretAccessKey ${RESPONSE_FILE}`
```

確認

```
cat << ETX

    ACCESS_KEY_ID:${ACCESS_KEY_ID}
    SECRET_ACCESS_KEY:${SECRET_ACCESS_KEY}
    
ETX
```

```

    ACCESS_KEY_ID:********************
    SECRET_ACCESS_KEY:****************************************

```

# 4．データのインポート

AWSが提供するサンプルデータをインポートする

http://docs.aws.amazon.com/ja_jp/redshift/latest/dg/c_sampledb.html

データをロードするためのコマンドを生成

```
LOAD_SAMPLE_DATA_FILE='load_sample_data.txt'

cat << EOF > ${LOAD_SAMPLE_DATA_FILE}
copy users from 's3://awssampledbuswest2/tickit/allusers_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' region 'us-west-2';

copy venue from 's3://awssampledbuswest2/tickit/venue_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' region 'us-west-2';

copy category from 's3://awssampledbuswest2/tickit/category_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' region 'us-west-2';

copy date from 's3://awssampledbuswest2/tickit/date2008_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' region 'us-west-2';

copy event from 's3://awssampledbuswest2/tickit/allevents_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region 'us-west-2';

copy listing from 's3://awssampledbuswest2/tickit/listings_pipe.txt' 
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}' 
delimiter '|' region 'us-west-2';

copy sales from 's3://awssampledbuswest2/tickit/sales_tab.txt'
credentials 'aws_access_key_id=${ACCESS_KEY_ID};aws_secret_access_key=${SECRET_ACCESS_KEY}'
delimiter '\t' timeformat 'MM/DD/YYYY HH:MI:SS' region 'us-west-2';

EOF
```

確認
（ここで出力したコマンドをpsqlでの接続後に使用します）

```
cat ${LOAD_SAMPLE_DATA_FILE}
```

クラスタに接続
（パスワード入力を要求されます）

```
psql -h ${REDSHIFT_ENDPOINT} -U ${MASTER_USER_NAME} -d ${DB_NAME} -p ${PORT}
```

テーブルを作成

```
create table users(
	userid integer not null distkey sortkey,
	username char(8),
	firstname varchar(30),
	lastname varchar(30),
	city varchar(30),
	state char(2),
	email varchar(100),
	phone char(14),
	likesports boolean,
	liketheatre boolean,
	likeconcerts boolean,
	likejazz boolean,
	likeclassical boolean,
	likeopera boolean,
	likerock boolean,
	likevegas boolean,
	likebroadway boolean,
	likemusicals boolean);

create table venue(
	venueid smallint not null distkey sortkey,
	venuename varchar(100),
	venuecity varchar(30),
	venuestate char(2),
	venueseats integer);

create table category(
	catid smallint not null distkey sortkey,
	catgroup varchar(10),
	catname varchar(10),
	catdesc varchar(50));

create table date(
	dateid smallint not null distkey sortkey,
	caldate date not null,
	day character(3) not null,
	week smallint not null,
	month character(5) not null,
	qtr character(5) not null,
	year smallint not null,
	holiday boolean default('N'));

create table event(
	eventid integer not null distkey,
	venueid smallint not null,
	catid smallint not null,
	dateid smallint not null sortkey,
	eventname varchar(200),
	starttime timestamp);

create table listing(
	listid integer not null distkey,
	sellerid integer not null,
	eventid integer not null,
	dateid smallint not null  sortkey,
	numtickets smallint not null,
	priceperticket decimal(8,2),
	totalprice decimal(8,2),
	listtime timestamp);

create table sales(
	salesid integer not null,
	listid integer not null distkey,
	sellerid integer not null,
	buyerid integer not null,
	eventid integer not null,
	dateid smallint not null sortkey,
	qtysold smallint not null,
	pricepaid decimal(8,2),
	commission decimal(8,2),
	saletime timestamp);
```

データのロード（結果）
（アクセスキーを含んだcopyコマンドを貼り付けます。）

```
INFO:  Load into table 'users' completed, 49990 record(s) loaded successfully.
INFO:  Load into table 'venue' completed, 202 record(s) loaded successfully.
INFO:  Load into table 'category' completed, 11 record(s) loaded successfully.
INFO:  Load into table 'date' completed, 365 record(s) loaded successfully.
INFO:  Load into table 'event' completed, 8798 record(s) loaded successfully.
INFO:  Load into table 'listing' completed, 192497 record(s) loaded successfully.
INFO:  Load into table 'sales' completed, 172456 record(s) loaded successfully.
```

ロード結果の確認

```
select count(*) from users;
select count(*) from venue;
select count(*) from category;
select count(*) from date;
select count(*) from event;
select count(*) from listing;
select count(*) from sales;
```

```
 count
-------
 49990
(1 row)

 count
-------
   202
(1 row)

 count
-------
    11
(1 row)

 count
-------
   365
(1 row)

 count
-------
  8798
(1 row)

 count
--------
 192497
(1 row)

 count
--------
 172456
(1 row)
```

以上