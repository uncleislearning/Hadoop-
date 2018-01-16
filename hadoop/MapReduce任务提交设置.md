#### MapReduce客户端任务提交设置

```java
    //运行环境配置
          Configuration conf = new Configuration();
          conf.set("fs.defaultFS", "hdfs://master:9000/");
          conf.set("mapreduce.app-submission.cross-platform", "true");
          
          //生成一个Job
          Job job = Job.getInstance(conf);
            
          //为Job设置Mapper
          job.setMapperClass(SplitMapper.class);
          
          //为Job设置Reducer
          job.setReducerClass(SplitReducer.class);

          //为Job设置Map阶段输出的key的类型
          job.setMapOutputKeyClass(Text.class);
          
          //为Job设置Map阶段输出的value的类型
          job.setMapOutputValueClass(LongWritable.class);

          //为Job设置Reduce阶段输出的key类型
          job.setOutputKeyClass(Text.class);
          
          //为Job设置Reduce阶段输出的value类型
          job.setMapOutputValueClass(LongWritable.class);
            
          
          
          String path = "hdfs://master:9000/user/hive/warehouse/hive.db/sogou500_part/year=2011/month=12/day=30/hour="+i;
          String out = "hdfs://master:9000/out_keyword_hour/out"+i;
         
          //指定任务的输入文件路径
          FileInputFormat.setInputPaths(job, new Path(path));
          
          //指定任务执行结果输出的路径
          FileOutputFormat.setOutputPath(job, new Path(out));

          
          //等待任务执行完毕
          job.waitForCompletion(true);

```

