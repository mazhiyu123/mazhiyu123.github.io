---
layout:     post
random-img: true
title:      MaxCompute SQL和Java之间的数据类型对应
date:       2017-08-16 17:01:00
author:     mazhiyu
description: MaxCompute SQL和Java之间的数据类型对应
keywords: MaxComputeSQL
tags:
    - MaxComputeSQL
    - UDF
---

MaxCompute SQL Type	| Java Type
--------------------| ---------
Tinyint	| java.lang.Byte
Smallint |	java.lang.Short
Int	| java.lang.Integer
Bigint |	java.lang.Long
Float	| java.lang.Float
Double	| java.lang.Double
Decimal	| java.math.BigDecimal
Boolean	| java.lang.Boolean
String	| java.lang.String
Varchar	| com.aliyun.odps.data.Varchar
Binary	| com.aliyun.odps.data.Binary
Datetime |	java.util.Date
Timestamp |	java.sql.Timestamp
Interval_year_month |	com.aliyun.odps.data.IntervalYearMonth
Interval_day_time	| com.aliyun.odps.data.IntervalDayTime
Array	| java.util.List
Map	| java.util.Map
Struct |	com.aliyun.odps.data.Struct

Java对应的数据类型需要使用包装类。  
如果datetime需要使用String来处理。  
SQL 中的 NULL 值通过 Java 中的 NULL 引用表示，因此 Java primitive type 是不允许使用的，因为无法表示 SQL 中的 NULL 值 。  