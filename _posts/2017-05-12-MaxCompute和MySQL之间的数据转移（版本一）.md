---
layout: post
title: "MaxCompute和MySQL之间的数据转移（版本一）"
date: 2017-05-12
excerpt: "MaxCompute和MySQL之间的数据转移（版本一）"
tags: [MaxCompute, MySQL, Linux, Shell]
comments: true
---


#### 辅助Shell和文件

MaxCompute和MySQL配置文件config

```
# mysql数据库配置
mysqlhost=192.168.102.10
mysqldb=taxi
mysqluser=root
mysqlpwd=1992

# MaxCompute配置
project_name=...
access_id=...
access_key=...
end_point=http://service.odps.aliyun.com/api
log_view_host=http://logview.odps.aliyun.com
https_check=true
# confirm threshold for query input size(unit: GB)
data_size_confirm=100.0
# this url is for odpscmd update
update_url=http://repo.aliyun.com/odpscmd

```

独立的函数集合文件 .functions.sh

```
#!/bin/bash
#定义了各种函数的脚本文件

fileconf=/usr/shell/config

function getconf {
	#echo `cat $fileconf | grep $1 | awk -F= '{print $2}'`
	#echo `cat $fileconf | grep $1 | sed 's/.*=\(.*$\)/\1/' `
	#echo `cat $fileconf | grep $1 | grep -oP '(?<==).*'`
	echo `cat $fileconf | grep $1 | cut -d= -f2`
}

```

#### MySQL数据转移到MaxCompute

```
#!/bin/bash
# 将数据从mysql转移到MaxCompute

if [ $# -ne 2 ]; then
	echo 'Usage: mysql_to_maxcompute mysql源数据表名 maxcompute目的表名'
	exit 1
fi

# 引入函数文件
. /usr/shell/functions.sh

fileconf=/usr/shell/config

srctable=$1
dsttable=$2

# 从配置文件中取得mysql相关配置
mysqlhost=$(getconf mysqlhost)
mysqldb=$(getconf db)
mysqluser=$(getconf mysqluser)
mysqlpwd=$(getconf mysqlpwd)

# 从配置文件中取得maxcompute相关配置
maxcp=$(getconf project_name)

#echo -e 'host: '$mysqlhost'\ndb: '$db' \nmysqluser: '$mysqluser' \nmysqlpwd:
 '$mysqlpwd 


#mysqldump -u$mysqluser -p$mysqlpasswd  -h$mysqlhost $mysqldb -d > /tmp/mysql
out/taxi_operaion_info_alldata.sql

mysql -u${mysqluser} -p$mysqlpwd   <<EOF

use $mysqldb;

select * from $srctable into outfile '/tmp/tmpdata.csv' fields terminated by 
',';

EOF

# 上面已经将数据从mysql中导出到/tmp下
# 下面准备将/tmp下的数据上传到maxcompute中

/usr/odpscmd_public/bin/odpscmd  <<EOF
tunnel upload /tmp/tmpdata.csv $dsttable;
EOF

```

#### MaxCompute数据转移到MySQL

```
#!/bin/bash
# 数据从maxcompute转移到mysql

if [ $# -ne f2 ]; then
	echo 'Usage: maxcompute_to_mysql maxcompute源数据表名 mysql目的表名'
	exit 1
fi

# 引入函数文件
. /usr/shell/functions.sh

srctable=$1
dsttable=$2

# 从配置文件中取得mysql相关配置
mysqlhost=$(getconf mysqlhost)
mysqldb=$(getconf db)
mysqluser=$(getconf mysqluser)
mysqlpwd=$(getconf mysqlpwd)

# 从配置文件中取得maxcompute相关配置
maxcp=$(getconf project_name)

# 从maxcompute下载数据到本地
/usr/odpscmd_public/bin/odpscmd  <<EOF
tunnel download  $srctable /tmp/maxdowntmp.csv;
EOF


mysql -u${mysqluser} -p$mysqlpwd   <<EOF

use $mysqldb;
load data local infile '/tmp/tmpdata.csv' into table taxi_operation_info
fields terminated by ',';
EOF
```