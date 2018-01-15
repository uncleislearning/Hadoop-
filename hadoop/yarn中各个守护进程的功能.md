#### NodeManager
>1. 与ResourceManager保持通信
>
>2. 管理Container的生命周期
>
>3. 监控Container的使用情况（内存、cpu ）
>
>4. NodeManager详解参考：http://dongxicheng.org/mapreduce-nextgen/nodemanager-architecture/

#### ResourceManager
>1. 资源分配。将集群的资源分配给各个应用程序
>
>2. 管理ApplicationMaster。接受ApplicationMaster的注册以及心跳信息（资源请求信息）
>
>3. 与NodeManager通信，要求其为某个应用程序启动ApplicationMaster
>
>4. 管理NodeManager
>
>5. 详细介绍参考：http://dongxicheng.org/mapreduce-nextgen/yarnmrv2-resource-manager-infrastructure/



#### ApplicationMaster
>ApplicationMaster是一个特别的库，对于不同的计算模型而言，它们都有自己的AM实现，比如MapReduce计算模型，Spark计算模型。ResourceManager 一个是全局的资源调度器，AM是具体某个应用的资源调度器，为应用向ResourceManager申请资源
>


#### Container
>1. Container是YARN中资源的抽象，它封装了某个节点上一定量的资源.
>
>2. Container由ApplicationMaster向ResourceManager申请的，由ResouceManager中的资源调度器异步分配给ApplicationMaster；
>
>3. Container的运行是由ApplicationMaster向资源所在的NodeManager发起的
>
>4. 详细参见：http://dongxicheng.org/mapreduce-nextgen/understand-yarn-container-concept/


#### 一个应用程序的执行流程：
>1.	 用户向ResourceManager提交应用程序
>
>2. ResourceManager为应用程序申请资源，然后要求某个NodeManager启动ApplicationMaster，ApplicationMaster注册到ResourceManager的监控列表
>
>3. ApplicationMaster与ResourceManager通信，为执行的应用程序申请资源，一旦申请到资源，与NodeManager通信，让它执行任务
>
>4. ApplicationMaster监控任务的执行直到结束，当任务执行失败，就申请新的资源（container）执行任务
>
>5. 当所有任务执行完毕，ApplicationMaster向ResourceManager注销