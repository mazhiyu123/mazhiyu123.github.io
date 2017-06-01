---
layout: post
title: "MaxCompute和MySQL之间的数据转移（版本二）"
date: 2017-05-12
excerpt: "MaxCompute和MySQL之间的数据转移（版本二）"
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
	val=`cat $fileconf | grep $1 | cut -d= -f2`
	if [ -n val ]; then
		echo $val
	else
		echo  '出错: '$1'的值为空'
	fi
}

```

#### 转移数据的主脚本文件

```
#!/bin/bash
# maxcompute和mysql之间的数据迁移

if [ $# -ne 3 ]; then
	echo "Usage: trans_data 源表 目的表 传输类型(只有两个值：[toMySQL, toMaxCompute] )"
	exit 1
fi

srctable=$1
dsttable=$2
transtype=$3

case $transtype in
	"toMySQL"      ) ./maxcompute_to_mysql.sh $srctable $dsttable;;
	"toMaxCompute" ) ./mysql_to_maxcompute.sh $srctable $dsttable;;
	* 	       ) echo "传输类型出错(只有两个值：[toMySQL, toMaxCompute] )";;
esac


```


#### MySQL数据转移到MaxCompute子功能脚本文件

```
#!/bin/bash
# 将数据从mysql转移到MaxCompute

if [ $# -ne 2 ]; then
	echo 'Usage: mysql_to_maxcompute mysql源数据表名 maxcompute目的表
名'
	exit 1
fi

# 引入函数文件
. /usr/shell/functions.sh

fileconf=/usr/shell/config

srctable=$1
dsttable=$2

echo "数据转移： MySQL表$srctable ===>> MaxCompute表$dsttable"

# 从配置文件中取得mysql相关配置
mysqlhost=$(getconf mysqlhost)
mysqldb=$(getconf db)
mysqluser=$(getconf mysqluser)
mysqlpwd=$(getconf mysqlpwd)

# 从配置文件中取得maxcompute相关配置
maxcp=$(getconf project_name)

#echo -e 'host: '$mysqlhost'\ndb: '$db' \nmysqluser: '$mysqluser' \nmysqlp
wd: '$mysqlpwd 

#mysqldump -u$mysqluser -p$mysqlpasswd  -h$mysqlhost $mysqldb -d > /tmp/my
sqlout/taxi_operaion_info_alldata.sql

if [ -f "/tmp/tmpdata.csv" ]; then
	rm -rf /tmp/tmpdata.csv
fi

outsql="select * from $srctable into outfile '/tmp/tmpdata.csv' fields ter
minated by ','"
mysql -u${mysqluser} -p$mysqlpwd -h$mysqlhost $mysqldb -e"${outsql}"  

# 上面已经将数据从mysql中导出到/tmp下
# 下面准备将/tmp下的数据上传到maxcompute中

if [ -f "/tmp/tmpdata.csv" ]; then
	odpscmd -e "tunnel upload /tmp/tmpdata.csv $dsttable;"
else 
	echo "没有mysql的数据导出文件tmpdata.csv"
	exit 1
fi

```

#### MaxCompute数据转移MySQL到子功能脚本文件

```
#!/bin/bash
# 数据从maxcompute转移到mysql

if [ $# -ne 2 ]; then
	echo 'Usage: maxcompute_to_mysql maxcompute源数据表名 mysql目的表
名'
	exit 1
fi

# 引入函数文件
. /usr/shell/functions.sh

srctable=$1
dsttable=$2

echo "数据转移： MaxCompute表$srctable ===>> MySQL表$dsttable"

# 从配置文件中取得mysql相关配置
mysqlhost=$(getconf mysqlhost)
mysqldb=$(getconf db)
mysqluser=$(getconf mysqluser)
mysqlpwd=$(getconf mysqlpwd)

# 从配置文件中取得maxcompute相关配置
maxcp=$(getconf project_name)

# 从maxcompute下载数据到本地
if [ -f "/tmp/maxdowntmp.csv" ]; then
	rm -rf /tmp/maxdowntmp.csv
fi
odpscmd -e "tunnel download $srctable /tmp/maxdowntmp.csv"

loadsql="load data local infile '/tmp/tmpdata.csv' into table taxi_operati
on_info fields terminated by ','"

if [ -f "/tmp/maxdowntmp.csv" ]; then
	mysql -u${mysqluser} -p${mysqlpwd} -h${mysqlhost} ${mysqldb} -e"${loadsql}"
else
	echo "没有maxcompute的数据导出文件maxdowntmp.csv"
	exit 1
fi

```