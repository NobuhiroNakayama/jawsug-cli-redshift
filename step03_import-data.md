


# 1．クライアントへの接続

## SSHで接続

```

```


# 2．クライアントツールのインストール

psqlを利用して接続します。

参考情報
http://docs.aws.amazon.com/ja_jp/redshift/latest/mgmt/connecting-from-psql.html

```

```



# 4．データのインポート

AWSが提供するサンプルデータをインポートする

アクセスキーを取得

データをロードするためのコマンドを生成

【アクセスキー部分を置き換える】
```
copy users from 's3://awssampledbuswest2/tickit/allusers_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' region 'us-west-2';

copy venue from 's3://awssampledbuswest2/tickit/venue_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' region 'us-west-2';

copy category from 's3://awssampledbuswest2/tickit/category_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' region 'us-west-2';

copy date from 's3://awssampledbuswest2/tickit/date2008_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' region 'us-west-2';

copy event from 's3://awssampledbuswest2/tickit/allevents_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' timeformat 'YYYY-MM-DD HH:MI:SS' region 'us-west-2';

copy listing from 's3://awssampledbuswest2/tickit/listings_pipe.txt' 
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>' 
delimiter '|' region 'us-west-2';

copy sales from 's3://awssampledbuswest2/tickit/sales_tab.txt'
credentials 'aws_access_key_id=<access-key-id>;aws_secret_access_key=<secret-access-key>'
delimiter '\t' timeformat 'MM/DD/YYYY HH:MI:SS' region 'us-west-2';
```


クラスタに接続
```

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


