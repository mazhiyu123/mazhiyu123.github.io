---
layout: post
title: "MaxComputeSQL常见问题CH1"
date: 2017-05-09
excerpt: "MaxComputeSQL常见问题CH1"
tags: [MaxComputeSQL]
comments: true
---


1. [如何更新和删除数据？](https://help.aliyun.com/knowledge_detail/40275.html?spm=5176.7840267.2.1.qeLPWB)

2. [MaxCompute 执行SQL报错:提示If you really want to perform this join, try mapjoin](https://help.aliyun.com/knowledge_detail/40268.html?spm=5176.7840267.2.2.qeLPWB)
3. [原来没有指定分区，可以增加或更改分区吗？](https://help.aliyun.com/knowledge_detail/40288.html?spm=5176.7840267.2.3.qeLPWB)  
在创建表的时候并没有进行分区（创建的是非分区表），此时是不能为表添加分区和更改分区操作的。

4. [如何快速导入数据到分析型数据库](https://help.aliyun.com/knowledge_detail/40269.html?spm=5176.7840267.2.4.qeLPWB)
5. [一个表中的分区数量越多越好吗？](https://help.aliyun.com/knowledge_detail/40289.html?spm=5176.7840267.2.5.qeLPWB)
6. [分区（Partition）是干什么用的？](https://help.aliyun.com/knowledge_detail/40290.html?spm=5176.7840267.2.6.qeLPWB)
7. [MaxCompute 的Mapjoin如何缓存多个小表?](https://help.aliyun.com/knowledge_detail/40270.html?spm=5176.7840267.2.7.qeLPWB)
8. [查看 MaxCompute 的数据量](https://help.aliyun.com/knowledge_detail/40271.html?spm=5176.7840267.2.8.qeLPWB)
9. INSERT语句执行过程中出现错误，会损坏原有数据吗？  
    不会损坏原有数据。MaxCompute 满足原子性(Atomicity），INSERT要么成功更新，要么失败回滚。
10. 可以添加或删除列吗？  
可以添加列，但不可能删除列。如果表中已经有了一部分数据，则该新添加列的值是NULL。  
添加列的语法是：ALTER TABLE table_name ADD COLUMNS (col_name1 type1, col_name2 type2...)
11. 如何查看 MaxCompute 分区中的数据量？  
    如果这个数据量是条数的话，您需要使用sql来进行count，比如您查table1这个表下的ds=’20160205’这个分区，您查条数，就需要
    ```
    select count(*) from table1 where ds=’20160205’;
    ```
    如果是要看占用空间大小，您可以用
    ```
    desc table1 partition(ds=’20160205’);
    ```
    这个空间占用是压缩过的大小。
12. [MySQL, Oracle的查询能否直接应用于MaxCompute SQL？](https://help.aliyun.com/knowledge_detail/40293.html?spm=5176.7840267.2.12.qeLPWB)
13. MaxCompute 跑SQL报错"Table xx has n columns， but query has m columns."  
    MaxCompute 的SQL使用INSERT INTO/OVERWRITE TABLE XXX SELECT 插入数据的时候，需要保证SELECT查询出来的字段和插入的表的字段，包括顺序、字段类型都能匹配，当然总的字段数量上也要能对的上。目前 MaxCompute 不支持指定插入表中某几个字段，其他字段为NULL或者其他默认值的情况，用户可以在SELECT的时候设置成NULL，比如SELECT ‘a’,NULL FROM XX。
14. [MaxCompute 接受由日志服务sls传输过来的日志文件,日志的内容采用json格式存储,如果日志的内容采用json格式存储，导入到表中如何解析？](https://help.aliyun.com/knowledge_detail/40274.html?spm=5176.7840267.2.14.qeLPWB)
15. 怎么查询一张表的所占内存大小？  
    用desc table命令可以查看，返回的size单位是字节。
16. udf可否在插入时支持条件判断？  
    udf可否在插入时支持，插入的时候判断一下，当某个值小于10时，加1插入，如果该值大于10，更改另一个字段的逻辑,如下：
if (ctx._source.views <10) {ctx._source.views =ctx._source.views+1;}else {ctx._source.overflow=1} ？
答：udf支持该判断。另外这个逻辑不一定要用udf实现，可以参考case when语法
    ```
    SELECT * FROM test;   
    a   
    ---   
    1   
    2   
    3   
    SELECT a,   
    CASE WHEN a=1 THEN 'one'   
    WHEN a=2 THEN 'two'   
    ELSE 'other'   
    END   
    FROM test;   
    a | case   
    ---+-------   
    1 | one   
    2 | two   
    3 | other 
    ```

17. [MaxCompute 通过SQL删除数据的方法](https://help.aliyun.com/knowledge_detail/40277.html?spm=5176.7840267.2.17.qeLPWB)
18. [MaxCompute 里分区和分区列的区别](https://help.aliyun.com/knowledge_detail/40278.html?spm=5176.7840267.2.18.qeLPWB)
19. 如何查看分区表的分区情况
    ```
    show partitions table_name;
    ```
20. [MaxCompute 删除表中的列字段的方法?](https://help.aliyun.com/knowledge_detail/40279.html?spm=5176.7840267.2.19.qeLPWB)
21. 用insert into…values...语句插入表记录报错，请问如何往 MaxCompute 表插入记录?  
    可以先创建一个表， 例如dual.
    ```
    create table dual(cnt bigint);
    insert into table dual select count(*) as cnt from dual;
    ```
    这样就生成了一张有1条记录的dual表。
如果您要插入一条记录的时候，可以  
    ```
    insert into table xxxx select 1,2,3 from dual;
    ```
    上面的语句相当于用1，2，3来向表中插入数据。
    
22. 