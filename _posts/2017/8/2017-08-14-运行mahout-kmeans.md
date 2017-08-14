layout:     post
random-img: true
title:      运行mahout-kmeans
date:       2017-08-14 12：26
author:     mazhiyu
description: 运行mahout-kmeans
keywords: 运行mahout-kmeans
tags:
    - Mahout
---


可以通过命令行的方式使用mahout中的算法

#### kmeans 命令及参数

```
mahout kmeans
Running on hadoop, using /usr/cluster/hadoop2.7.3/hadoop-2.7.3/bin/hadoop and HADOOP_CONF_DIR=
MAHOUT-JOB: /usr/cluster/mahout-0.13.0/examples/target/mahout-examples-0.13.2-SNAPSHOT-job.jar
17/08/08 10:59:53 ERROR AbstractJob: Missing required option --clusters
Missing required option --clusters                                              
Usage:                                                                          
 [--input <input> --output <output> --distanceMeasure <distanceMeasure>         
--clusters <clusters> --numClusters <k> --randomSeed <randomSeed1>              
[<randomSeed2> ...] --convergenceDelta <convergenceDelta> --maxIter <maxIter>   
--overwrite --clustering --method <method> --outlierThreshold                   
<outlierThreshold> --help --tempDir <tempDir> --startPhase <startPhase>         
--endPhase <endPhase>]                                                          
--clusters (-c) clusters    The input centroids, as Vectors.  Must be a         
                            SequenceFile of Writable, Cluster/Canopy.  If k is  
                            also specified, then a random set of vectors will   
                            be selected and written out to this path first      
17/08/08 10:59:53 INFO MahoutDriver: Program took 2143 ms (Minutes: 0.03571666666666667)
```

#### 文本文件转换为SequenceFile 

#### 运行kmeans

