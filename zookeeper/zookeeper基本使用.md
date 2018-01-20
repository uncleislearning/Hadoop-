#### Zookeeper介绍
>
>Zookeeper is a high-performance coordination service for distributed applications.
>
>
>主要解决多个进程之间的同步问题，使得进行“同步”有序的访问临界资源（核心即使分布式锁的实现）
>
>Zookeeper提供数据结构（Znode）、watcher机制以及原语来实现分布式锁
>服务端口：2181
>

#### standlone 模式的配置

修改conf/zoo.cfg，添加如下内容：

```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```


#### Zookeeper 基本使用

* 启动Zookeeper服务

```
bin/zkServer.sh start
```

* 使用命令行工具连接Zookeeper

```
bin/zkCli.sh -server 127.0.0.1:2181
```

* 交互shell命令使用（略）

```
[zkshell: 0] help
ZooKeeper host:port cmd args
        get path [watch]
        ls path [watch]
        set path data [version]
        delquota [-n|-b] path
        quit
        printwatches on|off
        createpath data acl
        stat path [watch]
        listquota path
        history
        setAcl path acl
        getAcl path
        sync path
        redo cmdno
        addauth scheme auth
        delete path [version]
        setquota -n|-b val path
```
注意：更新Zookeeper的操作是有限制的,delete或setData必须明确要更新的Znode的版本号，我们可以调用exists找到。如果版本号不匹配，更新将会失败。




