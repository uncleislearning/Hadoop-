#### map过程详解
>
>map的输出并不是简单的将结果写到磁盘。
>
>每个map任务都有一个环形内存缓冲区用于存储任务输出（默认100MB，缓存阈值为0.8）。
>
>当缓冲内容达到一定的阈值，一个后台线程开始将内容溢写到磁盘（按照轮询的方式将内容写到指定目录）。
>
>溢写磁盘的过程，map输出会继续写入缓冲区。（如果缓冲区已满，map则被阻塞）
>
>每次内存溢写（spill）时，都会产生一个溢出文件（spill file）
>
>当map任务完成之前，可能会产生多个溢出文件，最终溢出文件会被合并成一个已分区且已排序的输出文件（为什么是已排序、已分区的？因为在写磁盘之前，线程首先根据数据最终要传的reduer把数据划分成相应的分区，在每个分区中，后台线程按键进行内排序，如果有一个combiner，它就在排序后的输出上运行）（shuffle过程）
>
>最终map输出的文件位于运行map任务的本地磁盘
>

#### reduce过程详解
>
>reducer通过HTTP方式得到输出文件的分区
>
>一个reducer需要集群中多个map任务的输出作为输入。
>
>reducer需要将多个map任务的输出复制到本地。（复制阶段）
>
>如果map的输出比较小，会被复制到reduce任务的内存，否则，map输出被复制到磁盘。
>
>当磁盘上文件副本增多，后台线程会将它们合并成为更大的、排好序的文件
>
>复制完所有map的输出，reduce任务进行排序阶段(合并多个map的输出，并维持顺序)（合并排序阶段）
>
>
>最后，直接把数据输入reduce函数（reduce阶段）
>



#### shuffle过程详解
>
>shuffle过程已经蕴藏在上述过程中。
>
>MapReduce确保每个reducer的输入都是按照键排序的。系统将map的输出作为输入传给reducer时执行排序。所以shuffle过程主要的目的就是这个
>
>整体上，shuffle过程分为：
> 1. 分区partition（map溢写之前）
> 2. Sort根据key排序
> 3. Combiner进行局部value合并（在排序之后进行）


#### combiner详解
>
>
>每个map可能产生大量输出，combiner的作用就是在map端对输出先做一次合并，以减少传输到reducer的数据量
>
>combiner最基本是实现本地key的归并
>
>如果不使用combiner，那么所有结果都是在reduce中完成，效率会相对低下
>
>
>


#### partition详解
>
>map溢写之前，后台会有分区线程，根据reducer的数量对溢写文件进行分区。
>
>用户可以自己实现分区机制
>
>在需要让某个mapper的计算结果进入指定的reducer时，可以在Mapper端编写Partitioner
>
>默认时HashPartitioner
>
>确定该分区输入哪个reducer：
>
>which reducer =(key.hashCode() & Integer.MAX_VALUE) %numReducerTask
>
>
