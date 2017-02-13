---
published: true
layout: post
title: VirtualBox虚拟磁盘扩容
category: VirtualBox
tags: 
  - VirtualBox
  - Linux
time: 2017.02.13 11:29:00
excerpt: VirtualBox虚拟磁盘扩容
---

#### 虚拟机硬盘扩容

一. 方式一 命令行  
   1.  window下打开命令行窗口，cd到virtualbox的安装目录下，然后执行命令  VBoxManage modifyhd <到 vdi的路径> --resize <new size in megabytes> ，扩容1G则这里写1024.  
命令总是出错，这是因为原来的vdi是固定大小的所以不可以这样扩容。
D:\VirtualBox>VBoxManage  modifymedium  D:\VirtualBox\VB_Disk\Slave-3.vdi --resize 30720  
0%...
Progress state: VBOX_E_NOT_SUPPORTED
VBoxManage.exe: error: Resize medium operation for this format is not implemented yet!

二. 方式二 新建虚拟硬盘然后挂载  
1. 换一种方式在virtualbox中相应虚拟机，新建一个虚拟硬盘。（关机之后）。 开机启动，但是硬盘已满无法启动。需要删除一些东西。
   启动另一个虚拟机，将无法启动的虚拟机的硬盘挂在到这个虚拟机中删除一些东西。  
    开机后  
    fdisk -l  查看磁盘情况  
    挂在硬盘 (格式：mount 要挂在的硬盘  挂载点)  
    mount /dev/sdc  /newdisk  
    遇到两个提示  
    mount: /dev/sdc 写保护，将以只读方式挂载  
    mount: 未知的文件系统类型“(null)”    
    第二个提示，无法识别出文件系统来，但是又不能格式化。  
    待解决！！！  
    一种解决的方法！！！  
    linux开机的时候按e进入编辑模式  
    
    在.UTF-8 后面加入init=/bin/sh    进入sh。  

    在准备删除的时候遇到ready only system 的提示，无法删除，su - 进入root也不行。
    再使用fdisk mount 命令挂载新的虚拟硬盘，尝试将100%的硬盘的内容mv到新挂载的硬盘上，依然提示ready only system。
    应该是文件系统损坏，尝试用xfs_repair修复，依然失败，
    最后将Slave-3虚拟机重建了，加入到集群中。

    先给其他可以启动虚拟机扩容  
    fdisk -l 查看磁盘情况，找到要挂载的硬盘  
    在fdisk 挂载的硬盘  分区  
    fdisk /dev/sdb  
    输入m
    ```
    命令(输入 m 获取帮助)：m
    ```
    选择n，增加分区
    ```
     n   add a new partition
    ```
    选p主分区(e为扩展分区)
    ```
    命令(输入 m 获取帮助)：n
   Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
   ```
   默认即可
   ```
   分区号 (1-4，默认 1)：1
    起始 扇区 (2048-31457279，默认为 2048)：2048
    Last 扇区, +扇区 or +size{K,M,G} (2048-31457279，默认为     31457279)：
    将使用默认值 31457279
    分区 1 已设置为 Linux 类型，大小设为 15 GiB

   ```
   保存退出
   ```
   命令(输入 m 获取帮助)：w
    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    正在同步磁盘。
   ```
   再次fdisk -l,增加了信息
   ```
   设备 Boot      Start         End      Blocks   Id  System
    /dev/sdb1            2048    31457279    15727616   83  Linux
    ```
    
  分区建完，创建文件系统  
  (df -T 查看其他挂载的磁盘的文件系统，这里创建的文件系统可以和原来的一样也可以不一样
  )  
  mkfs -t ext4 /dev/sdb
  
  然后，挂载分区  
  mount /dev/sdb /newdisk
  输入df -T，增加一行信息
  ```
  /dev/sdb                ext4     15350728   40984 14506928    1% /newdisk
  ```
  
  再设置开机自动挂载
  ```
  vim /etc/fstab
  ```
  增加一行信息
  ```
  /dev/sdb  /newdisk          ext4    defaults 0   0
  ```
  