---
layout: post
title: "MaxComputeSQL常见问题CH2"
date: 2017-05-09
excerpt: "MaxComputeSQL常见问题CH2"
tags: [MaxComputeSQL]
comments: true
---

1. MaxCompute 查询数据的时候只能查询前5000条？怎么扫描全表 ？  
    现在结果集的返回最大是5k。如果数据集很大，建议用tunnel处理。

2. MaxCompute 进行 SQL 查询，使用 ?not?in? 后面接子查询,子查询数量是上万级别的，如何实现？  
    答：用 left outer join
    ```
    select from a where aid not in (select id from b)
    ```
    改成  
    ```
    select a. from a left outer join
    (select distinct id from b) bb
    on a.id=b.id 
    where b.id is null
    ```
3. UDAF和UDTF的区别是啥?  
    如果是需要多条数据整理成1条数据的，用udaf。如果是需要一条数据对应多条输出的，就用udtf。
4. [MaxCompute 支持在UDF中读取资源吗？](https://help.aliyun.com/knowledge_detail/40284.html?spm=5176.7840267.2.4.D8kNCS)
5. [怎么将1个表从1个project 同步到另一个Project？](https://help.aliyun.com/knowledge_detail/40286.html?spm=5176.7840267.2.6.D8kNCS)
6. 在RDS上是做过分库分表，数据量特别大，请问在 MaxCompute 上如何组织数据？  
    MaxCompute 是海量数据存储和处理服务，不需要做分库分表处理，结构相同的数据存储在一张 MaxCompute 表中就可以。但是为了提高运行性能，需要对大数据表做分区（Partition），例如原来是按天分表，在 MaxCompute 上则是每天一个分区。
7. MaxCompute表如何设置自增长列？  
    MaxCompute目前不支持自增长序列功能，如果确实有这样的需求，而且数据量比较小，可以考虑使用窗口函数row_number来实现。
8. 执行TO_DATE函数报错没有分钟部分  
    执行SQL：
    ```    
    to_date(‘2016-07-18 18:18:18’, ‘yyyy-MM-dd HH:mm:ss’)
    ```
    报错内容是
    ```
    FAILED: ODPS-0121095:Invalid arguments - format string has second part, but doesn’t have minute part : yyyy-MM-dd HH:mm:ss
    ```
    格式错了应该是yyyy-MM-dd HH:mi:ss.分钟的格式应该是mi而不是mm。

9. [执行SQL报错：隐式类型转换错误](https://help.aliyun.com/knowledge_detail/44233.html?spm=5176.7840267.2.10.D8kNCS)
10. 对Double类型数据进行比较过滤  
    由于MaxCompute(ODPS)的Double值存在一定的精度差，因此，我们不建议直接使用等号(=)对两个Double类型数据进行比较。建议用户对两个Double类型数据相减， 而后取绝对值的方式判断。当绝对值足够小的时候，认为两个Double数值相等。
11. 执行SQL报错输入表过多  
    执行MaxCompute SQL报错：FAILED: ODPS-0123065:Join exception - Maximum 16 join inputs allowed.  
    MaxCompute SQL支持的Join操作。支持最多6张小表的mapjoin以及连续join的表不能超过16张,
    如果确实有这样的需求，用户可以考虑把部分小表join成一张临时表，减少输入表的个数。
12. 执行SQL报错提示输出表分区过多  
    执行SQL报错：FAILED: ODPS-0123031:Partition exception - a single instance cannot output data to more than 2048 partitions  
    虽然目前单个MaxCompute的表，允许有6万个分区，但是单个任务涉及的输出的表分区的数量只有2048个。
    一般出现这个错误，是由于用户的分区字段设置有问题导致的，比如根据id字段做的分区。用户可以考虑修改分区表的分区键设置，避免这种问题。
13. [执行SQL报错 FAILED: ODPS-0010000:System internal error - OTS filtering exception - Ots read range partitions exceeds the specified limit:10000](https://help.aliyun.com/knowledge_detail/43152.html?spm=5176.7840267.2.14.D8kNCS)
14. [执行SQL报错：Repeated key in GROUP BY](https://help.aliyun.com/knowledge_detail/44230.html?spm=5176.7840267.2.15.D8kNCS)
15. [外关联后发现数据条数增加](https://help.aliyun.com/knowledge_detail/51055.html?spm=5176.7840267.2.17.D8kNCS)
16. [删除分区报错](https://help.aliyun.com/knowledge_detail/50937.html?spm=5176.7840267.2.18.D8kNCS)
17. [插入动态分区表常见问题](https://help.aliyun.com/knowledge_detail/50638.html?spm=5176.7840267.2.19.D8kNCS)
18. [MaxCompute 查询结果太大报错](https://help.aliyun.com/knowledge_detail/51021.html?spm=5176.7840267.2.20.D8kNCS)
19. MaxCompute SQL实现INSERT单条记录  
    目前还不支持类似 INSERT INTO TABLE xx VALUES(?,?,…)这样的语法。如果用户确实有这种插入一条数据的需求，可以使用以下方法：

    1. 创建一张表，在里面插入一条数据:
    ```
    CREATE TABLE Dual(Cnt Bigint);
    INSERT OVERWRITE TABLE Dual SELECT COUNT(*) FROM Dual;
    ```
    2. 使用INSERT INTO语法实现类似VALUES的用法，如：
    ```
    INSERT INTO TABLE TABLE_NAME SELECT 1,2,'a' FROM Dual;
    ```
20. [如何通过自定义日志打印对UDAF进行线上调试](https://help.aliyun.com/knowledge_detail/49783.html?spm=5176.7840267.2.2.uiQMCk)
