---
layout: post
title: "MaxCompute平台操作命令"
date: 2017-05-09
excerpt: "MaxCompute平台操作命令"
tags: [MaxCompute]
comments: true
---

#### 项目空间命令

进入某个项目空间
```
use project_name
```

#### Instance(实例)命令
[官方文档](https://help.aliyun.com/document_detail/27830.html?spm=5176.doc27829.6.563.k1NbO0)  

显示当前用户创建的实例

```
SHOW INSTANCES [FROM startdate TO enddate] [number]
```

查看某个实例的状态
```
STATUS <instance_id>;
```

杀死某个正在运行的实例

```
kill <instance_id>;
```

根据具体的实例ID获得作业信息

```
desc instance <instance_id>;
```

#### 资源操作命令

增加资源

```
add file <local_file> [as alias] [comment 'cmt'][-f];
add archive <local_file> [as alias] [comment 'cmt'][-f];
add table <table_name> [partition <(spec)>] [as alias] [comment 'cmt'][-f];
add jar <local_file.jar> [comment 'cmt'][-f];
```
说明：  
file/archive/table/jar 表明资源类型，资源类型的介绍请参考资源(Resource) ；  
local_file：表示本地文件所在路径。并以此文件名作为该资源名，资源名是资源的唯一标识；  
table_name：表示 MaxCompute 中的表名 。  
[PARTITION (spec)]：当添加的资源为分区表时，MaxCompute 仅支持将某个分区作为资源，不支持将整张分区表作为资源；  
alias：指定资源名，不加该参数时默认文件名为资源名。Jar 及 Py 类型资源不支持此功能；  
[comment ‘cmt’]：给资源添加注释；  
[-f]：当存在同名的资源时，此操作会覆盖原有资源；若不指定此选项，存在同名资源时，操作将失败。

删除指定资源

```
DROP RESOURCE <resource_name>;
```

列出资源

```
LIST RESOURCES;
```

下载资源

```
GET RESOURCE [<project name>:]<resource name> <path>;
```
不能下载table的资源


#### 函数命令

注册函数

```
CREATE FUNCTION <function_name> AS <package_to_class> USING <resource_list>;
```
说明：  
function_name：UDF 函数名，这个名字就是 SQL 中引用该函数所使用的名字 。  
package_to_class：如果是 java UDF，这个名字就是从顶层包名一直到实现 UDF 类名的 fully qualified class name 。这个名字必须用引号引起来 。  
resource_list：UDF 所用到的资源列表，这个里面必须包括 UDF 代码所在的资源。如果用户代码中通过 distributed cache 接口读取资源文件，这个列表中还得包括udf所读取的资源文件列表 。资源列表由多个资源名>组成，资源名之间由逗号(”,”)分隔 。资源列表必须用引号引起来 。  
使用示例：假设 Java UDF 类 org.alidata.odps.udf.examples.Lower 在 my_lower.jar 中，创建函数 my_lower：
```
CREATE FUNCTION test_lower AS 'org.alidata.odps.udf.examples.Lower'
    USING 'my_lower.jar';
```

注销函数

```
DROP FUNCTION <function_name>;
```

查看函数列表

```
list functions;                  --查看当前项目空间中的所有的自定义函数
ls functions -p my_project;      --查看指定项目空间 my_project 下的所有自定义函数
```