---
layout:     post
random-img: true
title:      结构化表达式
subtitle:   MaxCompute SQL的结构化表达式
date:       2017-06-05 10:02:31
author:     mazhiyu
description: MaxCompute SQL的结构化表达式
keywords: MaxComputeSQL
tags:
    - MaxComputeSQL
---

#### CASE WHEN 表达式
在SQL语句中，CASE WHEN表达式有两种书写方式
方式一：
```
case value
        when (_condition1) then result1
        when (_condition2) then result2
        ...
        else resultn
    end
```

方式二：

```
    case
        when (_condition1) then result1
        when (_condition2) then result2
        when (_condition3) then result3
        ...
        else resultn
    end
```
实例：

```
select  case 
    when shop_name is null then 'default_name'
    when shop_name like 'hang%' then 'zj_region'
    end as region
    from sale_detail;
    
select deptno,
       count(empno) as cnt,
       round(sum(case when job = 'CLERK' then 1 else 0 end)/count(empno), 2) as rate
from emp
group by deptno;
```

说明：  
如果result类型只有bigint，double，统一转double再返回；  
如果result类型中有string类型，统一转string再返回，如果不能转则报错(如boolean型)；  
除此之外不允许其它类型之间的转换；  


#### IF 表达式

这里的IF表达式和平常理解的IF表达式是有所不同的
```
if(testCondition, valueTrue, valueFalseOrNull)
```

参数说明：  
testCondition:要判断的表达式， boolean类型；  
valueTrue: 表达式testCondition为True的时候，返回的值。  
valueFalseOrNull：不满足表达式testCondition时2，返回的值，可以设为Null.返回值：返回值类型和参数valueTrue或者valueFalseOrNul的类型一致。  

实例：
```
select if(1 = 2, 100, 200);

运行结果
200
```