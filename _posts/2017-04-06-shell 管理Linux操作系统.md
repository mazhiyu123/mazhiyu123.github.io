---
published: true
layout: post
title: shell 管理Linux操作系统
category: Shell
tags: 
  - Linux
  - Shell
time: 2017.04.06 15:24:00
excerpt:   shell 管理Linux操作系统
---

## 监控磁盘情况  
 ### 使用du查看磁盘占用情况  

 命令参数 | 含义 | 实例
 ---------| ---- | ----
 -a | 递归的输出制定目录或多个目录中所有文件的统计结果
 -h | 以合适的单位显示磁盘占用情况
 -c | 会在最下面增加一行统计所有文件占用磁盘的情况
 -s | 只输出总共的磁盘占用情况
 -b | 以字节为单位
 -k | 以KB为单位
 -m | 以MB为单位
 -B | 以指定块大小为单位
 --exclude  | 在磁盘统计的目录中排除某部分文件 | du --exclude "*.txt" filepath
 --exclude-from | 从指定文件中读取要排除的目录| du --exclude-from EXCLUDE.txt filepath
 --max-depth    | 指定遍历的最大深度| du --max-depth 2 filepath
 
 找出指定目录最大的7个文件(包含目录)
 ```
 du -ak filepath | sort -nrk 1 | head -n 7
 ```
 如果不包含目录的话需要用到find
 ```
 find . -type f -exec du -k {} \; | sort -nrk 1 | head -n 7
 ```
 
 ### 使用df查看磁盘可用空间
 
 ## 计算命令的执行时间
 命令格式time COMMAND  
 由于linux系统默认情况下是调用的是内建的linux，因此如果需要使用time的另外功能需要使用绝对路径/usr/bin/time
 
 参数 | 含义 | 实例 
 -----| ---- |  ----
 -o | 将相关的统计信息写入到指定的文件中 | /usr/bin/time -o output.txt COMMAND
 -a | 在写入制定文件的时候不会影响文件中原有的内容 | /usr/bin/time -a -o output.txt COMMAND
 
 这个命令的最终统计结果涉及到三个时间
 real：命令从开始执行到结束执行的时间
 user: 进程花费在用户模式（内核之外）的CPU时间
 sys:  进程花费在内核的CPU时间
 
 ## 收集与当前用户，启动日志和启动故障的相关信息
 
 1. 获取当前登陆用户的相关信息
 
 ```
 命令：who
 ```
 该命令会返回登录名，用户所使用的TTY，登陆时间，登陆用户的远程主机名
 
 2. 获取登陆用户的更详细信息
 ```
 命令： w
 ```
 
 
 3. 列出当前系统登陆的用户列表
 ```
 命令： users
 ```
 
 如果一个用户打开多个终端则会出现多次，想要去除重复的用户可以使用
 ```
 命令： users | tr ' ' '\n' | sort | unique
 ```
 
 4. 查看系统已经运行了多长时间
 ```
 命令： uptime
 ```
 
 5. 获取上次启动以及用户登录会话的信息
 ```
 命令：last
 ```
 
 6. 获取单个用户登陆会话的信息
 ```
 命令： last USER
 ```
 
 7. 获取重启会话
 ```
 命令：last reboot
 ```
 
 8. 获取失败的用户登陆会话信息
 ```
 命令： lastb
 ```
 
 ####  计算1小时内进程的cpu占用情况
 ```
 #!/bin/bash
# 计算1小时内进程的cpu占用情况

SECS=120
UNIT_TIME=60

STEPS=$(( $SECS / $UNIT_TIME ))

echo Watching cpu usage ...;

for((i=0;i<STEPS;i++))
do
        ps -eo comm,pcpu | tail -n +2 >> /tmp/cpu_usage.$$
        sleep $UNIT_TIME 
done

echo cpu eaters:

cat /tmp/cpu_usage.$$ | \ 
        awk '
        { process[$1] += $2; }
        END{
           for(i in process)
           {
             printf("%-20s %s\n",i,process[i])
           }      
   }    '|sort -nrk 2 | head

rm /tmp/cpu_usage.$$  
 ```