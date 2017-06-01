---
layout: post
title: "MaxCompute一些使用问题CH1"
date: 2017-05-15
excerpt: "MaxCompute一些使用问题CH1"
tags: [MaxCompute]
comments: true
---

整理自[文档](https://help.aliyun.com/knowledge_list/40295.html?spm=5176.doc48973.6.752.upXmfZ)  

1. mapjoin中大表和小表可以互换位置吗？
    可以互换位置但是就变成了普通join，会降低性能

2. MaxCompute 运行报错：OpenJDK 64-Bit Server VM warning: Insufficient space for shared memory  
    MaxCompute 运行报错：OpenJDK 64-Bit Server VM warning: Insufficient space for shared memory file: 4528
Try using the -Djava.io.tmpdir= option to select an alternate temp location.   
 这个报错是表示临时文件的目录空间不够了，可以 df 查看下空间使用情况

3. Shell 或 Python 脚本中，如何执行 MaxCompute 命令？  
    MaxCompute 命令支持-f参数，可在脚本或其它程序中直接以“odps -f <文件路径> ”的方式支持 MaxCompute 命令。

4. 目前 MaxCompute 不支持访问外网，假如我需要做分布式处理并访问外网的话，有什么解决办法吗？或者有没有合适的云组件支持？  
    目前只能支持访问资源表和资源文件，不支持访问外部的资源，也没有类似的云组件。建议您把对应的请求封装在资源文件/资源表里，或者直接写在 MaxCompute 表里

5. [ODPS-0420095: Access Denied - Project ip white list is invalid](https://help.aliyun.com/knowledge_detail/40297.html?spm=5176.7840295.2.5.RyzkXr)  

6. [MaxCompute与关系型数据库有什么不同？](https://help.aliyun.com/knowledge_detail/40337.html?spm=5176.7840295.2.6.RyzkXr)   
    

7. [MaxCompute 授权报错lack of account provider](https://help.aliyun.com/knowledge_detail/40298.html?spm=5176.7840295.2.7.RyzkXr)
    

8. SLS(简单日志服务)数据归档到 MaxCompute 的实效性  
    我们承诺最晚 6 小时完成 MaxCompute 的归档。这里的 6 个小时是最长的时间，99.9% 情况下一小时内就能导入完成。  
    我们计划大约在 1 月份会把导入状态以 API 方式提供给用户，由用户自己决定

9. 数据上传到 MaxCompute 后，数据可靠性是怎样的？  
    MaxCompute 集群是三个副本存储，提供 99.99999999% 的数据可靠性。另外，在您正常使用 MaxCompute 期间，如果对表指定了生命周期，则满足条件后，会自动释放；如果没有指定，默认为永久保存

10. MaxCompute 的适用场景是什么？  
    大数据计算服务 (MaxCompute，原名 ODPS) ，适用于离线海量数据的处理、分析或挖掘，同时具有海量存储和大数据离线分析两种能力。  
    1. 它以集群形式处理 TB/PB 级别的海量数据，适用于超出单台服务器或单 RDS 实例处理能力的数据统计和分析 。
    2. 如果是数据量不大，在单 RDS 实例上能够完成，或是在线服务（例如要求准实时响应的检索）的情况，建议选用 RDS 或 OTS 服务 。

11. MaxCompute 的最大列数  
    目前 MaxCompute 单表可以存放的最大的列数为 1200 列。如果用户的列数超过限制，可以考虑
    1. 对数据进行降维，缩减到 1200 以内。
    2. 修改数据的保存方式，修改成诸如三元组或者稀疏/稠密矩阵

12. 在整个解决方案中，是如何使用 MaxCompute 的？
    MaxCompute 通常作为解决方案的一部分，与其它系统的交互过程如下：
    1. 把数据上传到 MaxCompute ；
    2. 通过 SQL 或 MR 任务进行数据分析/挖掘处理；
    3. 数据分析/挖掘结果存储到 MaxCompute 结果表；
    4. 把 MaxCompute 结果表导出到 RDS 数据库（或其它在线存储方案），以提供在线服务。

13. Datahub和Tunnel的使用场景的区别  
    Datahub用于实时上传数据的场景，主要用于流式计算的场景。数据上传后会保存到实时表里，后续会在几分钟内通过定时任务的形式同步到离线表里，供离线计算使用。

    Tunnel用于批量上传数据到离线表里，适用于离线计算的场景

14. MaxCompute 客户端配置因本地时间不对导致超时  
    如果用户使用 MaxCompute 客户端连接服务的时候，有类似如下报错：
    
    ```
    FAILED: ODPS-0410031:Authentication request expired - the expire time interval exceeds the max limitation: 900000, max_interval_date:900000,expire_date:2015-12-23T10:15:31.000Z,now_date:2015-12-23T02:16:00.000Z
    ```
    
    那是因为用户的本地时间和 MaxCompute 服务器上的时间不一样，相差超过15分钟，导致被服务器认为请求超时而拒绝服务。用户可以调整一下本地时间后重新打开客户端。

15. [MaxCompute 控制台下载数据返回 getTableDataCsv.json](https://help.aliyun.com/knowledge_detail/40341.html?spm=5176.7840295.2.16.RyzkXr)

16. MaxCompute 如何释放？
    登录到阿里云的用户中心-大技术计算服务服务-MaxCompute ，删除对应的项目即可，删除之前请做好数据的备份。


17. 如果用户在编写 UDAF 的过程中，出现报错"FAILED: ODPS-0140051:Invalid function - com.aliyun.odps.udf.impl.AnnotationParser$ParseError: @Resolve annotation not found."
    是因为要在编写UDAF的时候添加@Resolve注解

18. [MaxCompute 里查询出来的数据是根据什么排序的](https://help.aliyun.com/knowledge_detail/40302.html?spm=5176.7840295.2.19.RyzkXr)  
    

19. [MaxCompute 和 Emapreduce 有什么区别？功能、特性、适用场景有什么不同](https://help.aliyun.com/knowledge_detail/40323.html?spm=5176.7840295.2.20.RyzkXr)

