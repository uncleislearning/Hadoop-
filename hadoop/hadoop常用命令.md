## hadoop常用命令
#### 集群控制脚本：

* 	start-dfs.sh	[-upgrade|-rollback] [other options such as -clusterId]
>  功能介绍：启动DataNode、NameNode、SecondaryNameNode进程，开启支持HDFS文件系统功能。

*  stop-dfs.sh 
>  功能介绍：关闭DataNode、NameNode、SecondaryNameNode进程，关闭HDFS文件系统功能。

* start-yarn.sh 
> 功能介绍：启动NodeManager、ResourceManager进程，开启yarn资源调度的功能，这时才可以跑MapReduce任务

* stop-yarn.sh
> 功能介绍：关闭NodeManager、ResourceManager进程，关闭yarn资源调度的功能，这时所有跑在MapReduce任务之上的任务（如Hive）都将不能正常使用。

* start-all.sh
> 功能介绍：开启HDFS功能以及yarn的功能，相当于start-dfs.sh、start-yarn.sh的综合效果。

* stop-all.sh
> 功能介绍：关闭HDFS功能以及yarn的功能，相当于stop-dfs.sh、stop-yarn.sh的综合效果。

* start-all.sh
> 功能介绍：开启HDFS功能以及yarn的功能，相当于start-dfs.sh、start-yarn.sh的综合效果。

* hadoop-daemon.sh
> 功能介绍：动态增加/删除datanode节点可以使用 hadoop-daemon.sh start/stop datanode 


* yarn-daemon.sh
> 功能介绍：动态增加/删除NodeManager可以使用 yarn-daemon.sh start/stop nodemanager.通常需要将某个新加入集群的DataNode交由yarn来管理，这时就需要在新加入的datanode节点上执行 yarn-daemon.sh start nodemanager 来启动NodeManager进程

* mr-jobhistory-daemon.sh  start historyserver
> 启动mapreduce历史服务器，可以查看mapreduce作业历史执行情况


* start-balancer.sh
> 启动balance工具，用于平衡Hadoop中各个datanode的文件分布，以避免出现部分datanode磁盘占用效率高的问题。
> 影响hadoop balance工具的几个参数：
> 
>  -threshold 默认设置：10，参数取值范围：0-100，
> 参数含义：判断集群是否平衡的目标参数，每一个 datanode 存储使用
> 率和集群总存储使用率的差值都应该小于这个阀值 ，理论上，该参数设
> 置的越小，整个集群就越平衡，但是在线上环境中，hadoop集群在进行balance时，还在并发的进行数据的写入和删除，所以有可能无法到达设定的平衡参数值。
>
> -dfs.balance.bandwidthPerSec  默认设置：
> 1048576（1 M/S），参数含义：设置balance工具在运行中所能占用的>带宽，设置的过大可能会造成mapred运行缓慢



#### 使用集群功能的命令：

* hdfs namenode -format
> 功能介绍：格式化namenode

* hdfs dfsadmin -report
> 功能介绍：查看DataNode、NameNode节点状态以及容量使用情况

* hdfs dfsadmin -setBalancerBandwidth 67108864
> 功能介绍：负载均衡

* hdfs dfsadmin -refreshNodes
> 功能介绍：在namenode上刷新管理的所有datanode。通常当新的datanode加入的时，需要进行如上操作

* hdfs dfsadmin -savemode enter|leave|get
> 功能介绍：HDFS文件系统的savemode操作

* hadoop fs | hdfs dfs + hdfs文件系统支持的命令
> 功能介绍：hdfs dfs -ls / 列举hdfs／目录下的所有文件

* yarn node -list
> 功能介绍：列举yarn管理的节点

* mapred job -list 
> 功能介绍：列举Hadoop job列表

* mapped job -kill jobID
> 功能介绍：杀死某个任务


* hadoop fs -getmerge [fileinhdfs] [fileinlocal]
> 功能介绍：将HDFS路径下的多个文件合并到本地一个文件中



* 