##Hadoop集群搭建详解

#### 环境准备(为完全分布式集群搭建做准备，对每台机器做如下准备，以ubuntu为例)
>* 关闭防火墙 : ufw disable
>* 安装ssh并配置免密码登录（master节点免密码登录slaves节点以及master自己）

``` 
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
```
>
>
>* 修改hostname(/etc/hostname、/etc/hosts)
>* 下载安装JDK
>* 测试：各节点之间能够相互ping通、master节点能给ssh免密码登录所有节点、java -versoin
> 

#### 单机模式（假设本机hostname为master）

 配置hadoop-env.sh
	
```
# The java implementation to use.
export JAVA_HOME=/root/hadoop2.8/jdk1.8.0_144
```
	
配置Hadoop环境变量(/etc/profile) 

```
export HADOOP_HOME=/root/hadoop2.8/hadoop-2.8.2
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

配置hdfs-site.xml（namenode数据存放目录、datanode数据存放目录、副本冗余系数1）

```
<configuration>
	<property>
        <name>dfs.namenode.name.dir</name>
        <value>/root/hadoop2.8/data/name</value>
	</property>
	<property>
        <name>dfs.datanode.data.dir</name>
        <value>/root/hadoop2.8/data/data</value>
	</property>
	<property>
        <name>dfs.replication</name>
        <value>1</value>
	</property>
	<property>
        <name>dfs.secondary.http.address</name>
        <value>172.16.100.69:50090</value>
	</property>
</configuration>
```
配置 core-site.xml

```
<configuration>
<!--必要配置 -->
	<property>
        <name>fs.defaultFS</name>
        <value>hdfs://master:9000</value>
	</property>
	<property>
        <name>hadoop.tmp.dir</name>
        <value>/root/hadoop2.8/data/tmp</value>
   </property>
<!--可选配置 -->
	<property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
	</property>
	<property>
         <name>hadoop.proxyuser.root.groups</name>
          <value>*</value>
	</property>
</configuration>
```

#### 完全分布式模式
配置hadoop-env.sh

```
# The java implementation to use.
export JAVA_HOME=/root/hadoop2.8/jdk1.8.0_144
```

配置Hadoop环境变量(/etc/profile) 

```
export HADOOP_HOME=/root/hadoop2.8/hadoop-2.8.2
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```
配置hdfs-site.xml

```
<configuration>
	<property>
        <name>dfs.namenode.name.dir</name>
        <value>/root/hadoop2.8/data/name</value>
	</property>
	<property>
        <name>dfs.datanode.data.dir</name>
        <value>/root/hadoop2.8/data/data</value>
	</property>
	<property>
        <name>dfs.replication</name>
        <value>3</value>
	</property>
	<property>
        <name>dfs.secondary.http.address</name>
        <value>172.16.100.69:50090</value>
	</property>
</configuration>

```



配置 core-site.xml

```
<configuration>
<!--必要配置 -->
	<property>
        <name>fs.defaultFS</name>
        <value>hdfs://master:9000</value>
	</property>
	<property>
        <name>hadoop.tmp.dir</name>
        <value>/root/hadoop2.8/data/tmp</value>
   </property>
<!--可选配置 -->
	<property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
	</property>
	<property>
         <name>hadoop.proxyuser.root.groups</name>
          <value>*</value>
	</property>
</configuration>
```

配置mapred-site.xml

```
<configuration>
	<!--必要配置 -->
	<property>
        <name>mapreduce.framework.name</name> 
        <value>yarn</value>
	</property>

<!--可选配置 -->
	<property>
      <name>mapreduce.admin.map.child.java.opts</name>
      <value>-Xmx1024m</value>
 	</property>
 	<property>
      <name>mapreduce.admin.reduce.child.java.opts</name>
      <value>-Xmx1536m</value>
	 </property>
</configuration>

```

配置yarn-site.xml

```
<configuration>

<!-- Site specific YARN configuration properties -->
<property>
        <name>yarn.resourcemanager.hostname</name>
        <value>master</value>
</property>
<property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
</property>
</configuration>

```

