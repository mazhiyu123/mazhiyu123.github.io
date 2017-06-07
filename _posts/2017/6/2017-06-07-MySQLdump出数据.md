---
layout:     post
random-img: true
title:      MySQLdump出数据
date:       2017-06-07 18:56:00
author:     mazhiyu
description: MySQLdump出数据
keywords: MySQL
tags:
    - MySQL
---

#### 备份所有数据库

```
mysqldump --all-databases > /my/path/dstdump.dump
```

#### 备份指定数据库

```
mysqldump -uroot -psomepwd databasename > /my/backup/path/databasedata.dump;
```

#### 跨主机备份

```
mysqldump --host=srcHost --opt srcDB | mysql --host=dstHost -C dstDB
```

##### --opt使生成的dump文件中稍有不同：  
1. 见表语句包含drop table if exists tablename
2. insert之前包含一个锁表语句lock tables tablename write,insert之后包含unlock tables

 
##### -C 指定主机间数据传输过程中进行数据压缩

#### 只备份表结构

```
mysqldump -uroot -psomepwd -d taxi taxi_operation_info > /tmp/taxi_operation_info.sql

```

taxi为数据库名，taxi_operation_info为表名

#### 只备份指定表数据

```
mysqldump -uroot -psomepwd  some_db user_list  -T/tmp/ --fields-terminated-by=','
```

在/tmp/目录下面生成两个文件  
user_list.sql 和 user_list.txt

.sql的文件保存了表user_list的创建语句  
.txt的文件保存了表的每一行的数据，数据之间逗号分隔

加参数-t，会忽略表的创建语句.sql的文件为空

```
mysqldump -uroot -psomepwd  some_db user_list  -t -T/tmp/ --fields-terminated-by=','
```

