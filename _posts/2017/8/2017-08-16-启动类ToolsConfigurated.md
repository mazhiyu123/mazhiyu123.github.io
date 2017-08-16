---
layout:     post
random-img: true
title:      启动类ToolsConfigurated
date:       2017-08-16 16:35:00
author:     mazhiyu
description: 启动类ToolsConfigurated
keywords: Hadoop
tags:
    - Hadoop
---

MR程序的主函数实例

```
public class KmeansSecondarySortDriver extends Configured implements Tool {
    
    public static void main(String[] args) throws Exception {
        int returnStatus = ToolRunner.run(new Configuration(), new KmeansSecondarySortDriver(), args);
    }
    
    @Override
    public int run(String[] args) throws IOException, ClassNotFoundException, InterruptedException {

        Configuration conf = getConf();

        Job job = Job.getInstance();
        
        job.setJobName("KmeansSecondarySort");
        job.setJarByClass(KmeansSecondarySortDriver.class);
        job.setMapperClass(KmeansSecondarySortMapper.class);
        job.setReducerClass(KmeansSecondarySortReducer.class);
        
        job.setMapOutputKeyClass(TaxiIDDateKey.class);
        job.setMapOutputValueClass(Text.class);
        
        job.setPartitionerClass(TaxiIDKeyPartitioner.class);
        job.setGroupingComparatorClass(TaxiIDKeyGroupingComparatort.class);
        
        job.setOutputKeyClass(TaxiIDDateKey.class);
        job.setOutputValueClass(Text.class);
        
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        
        boolean status = job.waitForCompletion(true);
        return status ? 0 : 1;
    }
    
}
```

主函数需要继承Configured类，实现Tool接口，相关类图如下

![image](http://od4ghyr10.bkt.clouddn.com/hadoop/srcread/%E5%90%AF%E5%8A%A8%E7%B1%BBToolsConfigurated.jpeg)  

