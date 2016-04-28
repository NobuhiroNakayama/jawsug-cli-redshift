








# 1. システムテーブルをクエリする

テーブル名のリストを表示する

```
select distinct(tablename) from pg_table_def where schemaname = 'public';
```

```
 tablename
-----------
 category
 date
 event
 listing
 sales
 users
 venue
(7 rows)
```

データベースユーザーを表示する

```
select * from pg_user;
```

```
 usename | usesysid | usecreatedb | usesuper | usecatupd |  passwd  | valuntil | useconfig
---------+----------+-------------+----------+-----------+----------+----------+-----------
 rdsdb   |        1 | t           | t        | t         | ******** | infinity |
 awsuser |      100 | t           | t        | f         | ******** |          |
(2 rows)
```

最近のクエリを表示する

```
select query, pid, elapsed, substring from svl_qlog
where userid = 100
order by starttime desc
limit 5;
```

```
 query |  pid  | elapsed |           substring
-------+-------+---------+-------------------------------
   737 | 32764 |    5577 | select count(*) from sales;
   695 | 32764 |  825968 | select count(*) from sales;
   694 | 32764 |    5337 | select count(*) from listing;
   693 | 32764 |  802109 | select count(*) from event;
   692 | 32764 |  835171 | select count(*) from date;
(5 rows)
```

実行中のクエリのプロセス ID を調べる

```
select pid, user_name, starttime, query
from stv_recents
where status='Running';
```

```
 pid | user_name | starttime | query
-----+-----------+-----------+-------
(0 rows)
```

# 2. ユーザデータをクエリする

テーブル名のリストを表示する

```

```

```

```

テーブルのスキーマを確認する

```

```

```

```

クエリを実行する



```
select * from users limit 10;
```

```
 userid | username | firstname | lastname |     city     | state |                    email                    |     phone      | likesports | liketheatre | likeconcerts | likejazz | likeclassical | likeopera | likerock | likevegas | li
kebroadway | likemusicals
--------+----------+-----------+----------+--------------+-------+---------------------------------------------+----------------+------------+-------------+--------------+----------+---------------+-----------+----------+-----------+---
-----------+--------------
      2 | PGL08LJI | Vladimir  | Humphrey | Murfreesboro | SK    | Suspendisse.tristique@nonnisiAenean.edu     | (783) 492-1886 |            |             |              | t        | t             |           |          | t         | f
           | t
      4 | XDZ38RDD | Barry     | Roy      | Omaha        | AB    | sed@lacusUtnec.ca                           | (355) 452-8168 | f          | t           |              | f        |               |           |          |           |
           | f
      5 | AEB55QTM | Reagan    | Hodge    | Forest Lake  | NS    | Cum@accumsan.com                            | (476) 519-9131 |            |             | t            | f        |               |           | t        | t         | f
           | t
      7 | OWY35QYB | Tamekah   | Juarez   | Moultrie     | WV    | elementum@semperpretiumneque.ca             | (297) 875-7247 |            |             |              | t        | t             | f         |          |           | f
           | f
      9 | MSD36KVR | Mufutau   | Watkins  | Port Orford  | MD    | Integer.mollis.Integer@tristiquealiquet.org | (725) 719-7670 | t          | f           |              | f        | t             |           |          |           | f
           | t
     10 | WKW41AIW | Naida     | Calderon | Waterbury    | MB    | Donec.fringilla@sodalesat.org               | (197) 726-8249 | f          | f           | f            |          | f             | t         |          | t         |
           |
     15 | OWU78MTR | Scarlett  | Mayer    | Gadsden      | GA    | lorem.ipsum@Vestibulumante.com              | (189) 882-8412 | t          | f           | t            |          |               | t         |          |           | t
           |
     16 | ZMG93CDD | Kieran    | Drake    | Hot Springs  | BC    | molestie.tellus@dapibusgravidaAliquam.com   | (192) 914-0016 |            | t           | t            |          | f             |           | t        | t         |
           | f
     18 | VDP05MXU | Germaine  | Valdez   | Kokomo       | WY    | cursus.Integer@arcuVestibulumante.com       | (998) 879-8668 |            | t           | t            |          | t             | t         |          | f         | t
           | t
     19 | CXQ97IWP | Amal      | Landry   | Lomita       | NT    | euismod@turpis.org                          | (891) 526-1468 |            | f           | t            |          | t             |           |          | f         | f
           | t
(10 rows)
```



```
select * from venue limit 10;
```

```
 venueid |         venuename          |   venuecity   | venuestate | venueseats
---------+----------------------------+---------------+------------+------------
       2 | Columbus Crew Stadium      | Columbus      | OH         |          0
       4 | CommunityAmerica Ballpark  | Kansas City   | KS         |          0
       5 | Gillette Stadium           | Foxborough    | MA         |      68756
       7 | BMO Field                  | Toronto       | ON         |          0
       9 | Dick's Sporting Goods Park | Commerce City | CO         |          0
      10 | Pizza Hut Park             | Frisco        | TX         |          0
      15 | McAfee Coliseum            | Oakland       | CA         |      63026
      16 | TD Banknorth Garden        | Boston        | MA         |          0
      18 | Madison Square Garden      | New York City | NY         |      20000
      19 | Wachovia Center            | Philadelphia  | PA         |          0
(10 rows)
```



```
select * from category limit 10;
```

```
 catid | catgroup | catname  |             catdesc
-------+----------+----------+---------------------------------
     2 | Sports   | NHL      | National Hockey League
     4 | Sports   | NBA      | National Basketball Association
     5 | Sports   | MLS      | Major League Soccer
     7 | Shows    | Plays    | All non-musical theatre
     9 | Concerts | Pop      | All rock and pop music concerts
    10 | Concerts | Jazz     | All jazz singers and bands
     1 | Sports   | MLB      | Major League Baseball
     3 | Sports   | NFL      | National Football League
     6 | Shows    | Musicals | Musical theatre
     8 | Shows    | Opera    | All opera and light opera
(10 rows)
```



```
select * from date limit 10;
```

```
 dateid |  caldate   | day | week | month |  qtr  | year | holiday
--------+------------+-----+------+-------+-------+------+---------
   1827 | 2008-01-01 | WE  |    1 | JAN   | 1     | 2008 | t
   1831 | 2008-01-05 | SU  |    2 | JAN   | 1     | 2008 | f
   1836 | 2008-01-10 | FR  |    2 | JAN   | 1     | 2008 | f
   1837 | 2008-01-11 | SA  |    3 | JAN   | 1     | 2008 | f
   1840 | 2008-01-14 | TU  |    3 | JAN   | 1     | 2008 | f
   1843 | 2008-01-17 | FR  |    3 | JAN   | 1     | 2008 | f
   1845 | 2008-01-19 | SU  |    4 | JAN   | 1     | 2008 | f
   1846 | 2008-01-20 | MO  |    4 | JAN   | 1     | 2008 | f
   1847 | 2008-01-21 | TU  |    4 | JAN   | 1     | 2008 | f
   1849 | 2008-01-23 | TH  |    4 | JAN   | 1     | 2008 | f
(10 rows)
```



```
select * from event limit 10;
```

```
 eventid | venueid | catid | dateid |     eventname     |      starttime
---------+---------+-------+--------+-------------------+---------------------
    1217 |     238 |     6 |   1827 | Mamma Mia!        | 2008-01-01 20:00:00
    1433 |     248 |     6 |   1827 | Grease            | 2008-01-01 19:00:00
    2811 |     207 |     7 |   1827 | Spring Awakening  | 2008-01-01 15:00:00
    3915 |      51 |     9 |   1827 | Return To Forever | 2008-01-01 14:00:00
    4135 |      16 |     9 |   1827 | Nas               | 2008-01-01 14:30:00
    5807 |      45 |     9 |   1827 | Return To Forever | 2008-01-01 15:00:00
    6649 |       6 |     9 |   1827 | Hannah Montana    | 2008-01-01 19:30:00
    6711 |      72 |     9 |   1827 | Smashing Pumpkins | 2008-01-01 19:00:00
    7747 |       6 |     9 |   1827 | K.D. Lang         | 2008-01-01 15:00:00
     521 |     248 |     6 |   1828 | West Side Story   | 2008-01-02 15:00:00
(10 rows)
```



```
select * from listing limit 10;
```

```
 listid | sellerid | eventid | dateid | numtickets | priceperticket | totalprice |      listtime
--------+----------+---------+--------+------------+----------------+------------+---------------------
    614 |    25339 |     770 |   1827 |         10 |         236.00 |    2360.00 | 2008-01-01 05:07:30
    776 |    20797 |    1811 |   1827 |         18 |         133.00 |    2394.00 | 2008-01-01 06:59:39
   2092 |    42560 |    8609 |   1827 |         22 |         194.00 |    4268.00 | 2008-01-01 05:49:06
   2688 |    10629 |    8791 |   1827 |          3 |          20.00 |      60.00 | 2008-01-01 09:18:40
   5736 |    32170 |    1221 |   1827 |          8 |         221.00 |    1768.00 | 2008-01-01 11:20:20
   6635 |    30023 |    2426 |   1827 |          8 |          50.00 |     400.00 | 2008-01-01 07:09:40
   7786 |    47561 |     117 |   1827 |          6 |         124.00 |     744.00 | 2008-01-01 02:12:34
   8301 |    45285 |    8582 |   1827 |          4 |          71.00 |     284.00 | 2008-01-01 08:09:16
   8434 |    46015 |    3937 |   1827 |          4 |         233.00 |     932.00 | 2008-01-01 08:51:49
   8942 |    23600 |    2557 |   1827 |         12 |         105.00 |    1260.00 | 2008-01-01 07:20:48
(10 rows)
```



```
select * from sales limit 10;
```

```
 salesid | listid | sellerid | buyerid | eventid | dateid | qtysold | pricepaid | commission |      saletime
---------+--------+----------+---------+---------+--------+---------+-----------+------------+---------------------
   33095 |  36572 |    30047 |     660 |    2903 |   1827 |       2 |    234.00 |      35.10 | 2008-01-01 09:41:06
   88268 | 100813 |    45818 |     698 |    8649 |   1827 |       4 |    836.00 |     125.40 | 2008-01-01 07:26:20
  110917 | 127048 |    37631 |     116 |    1749 |   1827 |       1 |    337.00 |      50.55 | 2008-01-01 07:05:02
  150314 | 173969 |    48680 |     816 |    8762 |   1827 |       2 |    688.00 |     103.20 | 2008-01-01 03:50:02
  157751 | 206999 |     3003 |     157 |    6605 |   1827 |       1 |   1730.00 |     259.50 | 2008-01-01 12:50:55
    1134 |   1176 |    37614 |     301 |    5414 |   1828 |       1 |    218.00 |      32.70 | 2008-01-02 08:22:32
    1924 |   2067 |    27144 |     256 |    6977 |   1828 |       2 |    494.00 |      74.10 | 2008-01-02 01:11:16
    8325 |   8942 |    23600 |    1078 |    2557 |   1828 |       5 |    525.00 |      78.75 | 2008-01-02 05:27:54
   40331 |  45102 |    13005 |    1091 |    1756 |   1828 |       2 |     58.00 |       8.70 | 2008-01-02 05:57:53
   40741 |  45570 |    18692 |     226 |    4650 |   1828 |       3 |     90.00 |      13.50 | 2008-01-02 01:40:59
(10 rows)
```

しばし、クエリをお楽しみください。

# 3. バキューム、分析

バキューム

```
vacuum;
```

```
VACUUM
```

分析

```
analyze;
```

```
ANALYZE
```

以上