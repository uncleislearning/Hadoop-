##### 启动服务(先启动zookeeper服务、再启动Kafka服务)

```
bin/zookeeper-server-start.sh config/zookeeper.properties

```


```
bin/kafka-server-start.sh config/server.properties 
```

##### 创建topic

```
bin/kafka-topic.sh --create --zookeeper localhost:2181 --replication-factor 1  --partitions 1 --topic test
```


##### 查看消息

```
bin/kafka-topics.sh --list --zookeeper localhost:2181

```

##### 消费消息

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test 
```
