---
layout:     post
random-img: true
title:      CentOS7安装mysql
date:       2017-06-01 08:47:00
author:     mazhiyu
description: CentOS7安装mysql
keywords: MySQL
tags:
    - MySQL
    - Linux
---

#### yum 安装

```
yum install mysql
yum install mysql-devel
yum install mysql-server

```

在安装mysql-server时候，由于yum库中没有，需要换一种方式。  

#### 官网下载mysql-server然后安装

如果提示没有wget命令，可是使用命令安装

```
yum -y install wget
```

下载mysql-server

```
wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

rpm -ivh mysql-community-release-el7-5.noarch.rpm

yum install mysql-community-server
```

#### 启动mysqld服务

```
systemctl start mysqld.service

```

使用命令netstat可以看到3306端口已经被mysqld监听

```
netstat -anp  | grep 3306
tcp6       0      0 :::3306                 :::*                    LISTEN      11362/mysqld
```

初次安装mysql，root账户是没有密码密码的

```
mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.6.36 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```

进入mysql后设置密码

```
use mysql;
UPDATE user SET Password = PASSWORD('newpass') WHERE user = 'root';
FLUSH PRIVILEGES;
```