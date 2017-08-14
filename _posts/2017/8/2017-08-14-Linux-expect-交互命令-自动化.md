---
layout:     post
random-img: true
title:      Linux-expect-交互命令-自动化
date:       2017-08-14 12:37:00
author:     mazhiyu
description: Linux-expect-交互命令-自动化
keywords: Shell
tags:
    - Shell
    - Linux
---

#### 安装expect

```
yum install -y expect
```
#### 主要关键字

关键字 | 作用
-------|-----
send | 用于向进程发送字符串
expect | 从进程接收字符串
spawn | 启动新的进程
interact | 允许用户交互

#### 常用模式

##### 单一分支模式语法

```
expect "hello" {send "world"}
```

匹配到hello后，会输出"world"

##### 多分支模式语法

```
expect "hello" { send "world\n" } \
"connect" { send "now connection\n" } \
"bye" { send "bye bye\n" }
```

匹配到hello,connect,bye任意一个字符串时，执行相应的输出。等同于如下写法：

```
expect {
"hi" { send "You said hi\n"}
"hello" { send "Hello yourself\n"}
"bye" { send "That was unexpected\n"}
}
```

#### 实例：登陆到远程主机压缩打包文件然后传输到本地

```
/usr/bin/expect << EOF
set time 30
spawn ssh -p22 $vps_ssh_user@$vps_host
expect {
"*password:" { send "$vps_ssh_pwd\r" }
}
expect "#*"
send "cd $vps_upload_path$dateymd\r"
send "find ./ -ctime -1 -exec tar czvf /tmp/upload_data_$dateymd.tar.gz {} \\\;\r"

spawn scp $vps_ssh_user@$vps_host:/tmp/upload_data_$dateymd.tar.gz $cluster_upload_path 
expect {
"*password:" { send "$vps_ssh_pwd\r" }
}
send "exit\r "
expect eof
EOF

```