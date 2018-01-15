#### NameNode
> 1. 管理文件系统命名空间
> 
> 2. 维护文件系统元数据
> 
> 3. 接收客户端请求
> 
> 4. 协调DataNode的工作
> 
> 5. 维护每个文件中各个块所在的数据节点的信息
> 
> 问题一：NameNode维护的数据信息以什么形式存在？
> 
> 答：NameNode维护的文件系统元数据信息以命名空间镜像文件fsimage和编辑日志文件edits存在。
> 
> 问题二：NameNode的容错机制有什么方式？
>
>答：第一种机制是备份那些组成文件系统元数据持久状态的文件。第二种机制运行SecondaryNameNode定期将edits文件中的操作合并到fsimage文件，它会保持合并后的命名空间镜像的副本，当NameNode发生故障时启用。第三种利用zookeeper搭建NameNode高可用集群，每个时刻只有一个主NameNode，其他辅NameNode同步主NameNode上的保持的数据，当主NameNode出现故障down机，由zookeeper从辅NameNode中选择一个节点当作主NameNode。第四种HDFS联邦使用多个NameNode（多个namespace），NameNode之间是相互独立的，共享DataNode的存储设备，DataNode 注册到集群所有NameNode上

#### DataNode
>1. 文件系统的工作节点，读写文件
>
>2. 定期向NameNode发送心跳信息，报告状态


#### SecondaryNameNode
>1. 定期合并NameNode中的edits文件和fsimage文件
>
>2. 同步NameNode，充当数据备份
>

