---
title: kafka的安装
toc: true
tags: 编程
categories:
  - 编程
  - kafka
abbrlink: 50337
date: 2018-10-20 19:26:08
---

# kafka的安装

## 1.下载安装

>kafka的安装需要依赖zookeeper，虽然kafka自带了zookeeper，但是为了方便维护，推荐自己安装zookeeper

```
#zookeeper下载地址：
https://zookeeper.apache.org/releases.html

#解压缩zookeeper
tar -zxvf apache-zookeeper-3.6.2-bin.tar.gz

##修改配置 conf/zoo.cfg
dataDir=/data/zk/node1/data
clientPort=2181

server.1=localhost:2287:3387
server.2=localhost:2288:3388
server.3=localhost:2289:3389


echo 1 >> /data/zk/node1/data/myid
echo 2 >> /data/zk/node2/data/myid
echo 3 >> /data/zk/node3/data/myid

##启动zookeeper
./zkServer.sh start ../conf/zoo1.cfg
./zkServer.sh start ../conf/zoo2.cfg
./zkServer.sh start ../conf/zoo3.cfg

#kafka下载地址：
http://kafka.apache.org/downloads

# 解压缩
tar -zxvf kafka_2.11-2.2.2.tgz

#修改 conf/server.properties
broker.id=0
listeners=PLAINTEXT://hostname:9092
log.dirs=/data/kafka-logs/kafka3
zookeeper.connect=zk1:2181,zk3:2181,zk3:2181


#启动
./kafka-server-start.sh -daemon ../config/server.properties


```


## 2.配置文件修改

```

broker.id=0  #当前机器在集群中的唯一标识，和zookeeper的myid性质一样

port=19092 #当前kafka对外提供服务的端口默认是9092

host.name=192.168.7.100 #这个参数默认是关闭的，在0.8.1有个bug，DNS解析问题，失败率的问题。

num.network.threads=3 #这个是borker进行网络处理的线程数

num.io.threads=8 #这个是borker进行I/O处理的线程数

log.dirs=/opt/kafka/kafkalogs/ #消息存放的目录，这个目录可以配置为“，”逗号分割的表达式，上面的num.io.threads要大于这个目录的个数这个目录，如果配置多个目录，新创建的topic他把消息持久化的地方是，当前以逗号分割的目录中，那个分区数最少就放那一个

socket.send.buffer.bytes=102400 #发送缓冲区buffer大小，数据不是一下子就发送的，先回存储到缓冲区了到达一定的大小后在发送，能提高性能

socket.receive.buffer.bytes=102400 #kafka接收缓冲区大小，当数据到达一定大小后在序列化到磁盘

socket.request.max.bytes=104857600 #这个参数是向kafka请求消息或者向kafka发送消息的请请求的最大数，这个值不能超过java的堆栈大小

num.partitions=1 #默认的分区数，一个topic默认1个分区数

log.retention.hours=168 #默认消息的最大持久化时间，168小时，7天

message.max.byte=5242880  #消息保存的最大值5M

default.replication.factor=2  #kafka保存消息的副本数，如果一个副本失效了，另一个还可以继续提供服务

replica.fetch.max.bytes=5242880  #取消息的最大直接数

log.segment.bytes=1073741824 #这个参数是：因为kafka的消息是以追加的形式落地到文件，当超过这个值的时候，kafka会新起一个文件

log.retention.check.interval.ms=300000 #每隔300000毫秒去检查上面配置的log失效时间（log.retention.hours=168 ），到目录查看是否有过期的消息如果有，删除

log.cleaner.enable=false #是否启用log压缩，一般不用启用，启用的话可以提高性能

zookeeper.connect=192.168.7.100:12181,192.168.7.101:12181,192.168.7.107:1218 #设置zookeeper的连接端口

```

## 3.启动命令



```

#启动

./kafka-server-start.sh -daemon ../config/server.properties


kafka1/bin/kafka-server-start.sh -daemon kafka1/config/server.properties
kafka2/bin/kafka-server-start.sh -daemon kafka2/config/server.properties
kafka3/bin/kafka-server-start.sh -daemon kafka3/config/server.properties

#暂停
kafka1/bin/kafka-server-stop.sh
kafka2/bin/kafka-server-stop.sh
kafka3/bin/kafka-server-stop.sh


#创建Topic

./kafka-topics.sh --create --zookeeper 192.168.7.100:12181 --replication-factor 2 --partitions 1 --topic shuaige

#解释

--replication-factor 2   #复制两份

--partitions 1 #创建1个分区

--topic #主题为shuaige



'''在一台服务器上创建一个发布者'''

#创建一个broker，发布者

./kafka-console-producer.sh --broker-list 192.168.7.100:19092 --topic shuaige



#在一台服务器上创建一个订阅者

./kafka-console-consumer.sh --zookeeper localhost:12181 --topic shuaige --from-beginning



#查看topic

./kafka-topics.sh --list --zookeeper localhost:12181





#查看topic状态

/kafka-topics.sh --describe --zookeeper localhost:12181 --topic shuaige

#下面是显示信息

Topic:ssports    PartitionCount:1    ReplicationFactor:2    Configs:

    Topic: shuaige    Partition: 0    Leader: 1    Replicas: 0,1    Isr: 1

#分区为为1  复制因子为2   他的  shuaige的分区为0 

#Replicas: 0,1   复制的为0，1



```





## 4.kafka日志



>可以通过配置server.properties文件中配置log.dirs参数来指定kafka的日志地址



```

server.log #kafka的运行日志

state-change.log  #kafka他是用zookeeper来保存状态，所以他可能会进行切换，切换的日志就保存在这里



controller.log #kafka选择一个节点作为“controller”,当发现有节点down掉的时候它负责在游泳分区的所有节点中选择新的leader,这使得Kafka可以批量的高效的管理所有分区节点的主从关系。如果controller down掉了，活着的节点中的一个会备切换为新的controller.

```



## 5.kafka的一些基本概念



- consumer 消费者：从消息队列中请求消息的客户端应用程序

- producer 生产者：向broker发布消息的应用程序

- broker AMQP服务端：用来接收生产者发送的消息并将这些消息路由给服务器中的队列，便于kfafka将生产者发送的消息，动态的添加到磁盘并给每一条消息一个偏移量，所以对于kafka一个broker就是一个应用程序的实例,即一个kafka节点。

- partition 分区：一个Topic中的消息数据按照多个分区组织，分区是kafka消息队列组织的最小单位，一个分区可以看作是一个FIFO（ First Input First Output的缩写，先入先出队列）的队列。

- topic 主题：一个主题类似新闻中的体育、娱乐、教育等分类概念，在实际工程中通常一个业务一个主题

- replication factor 副本因子：推送到kafka的消息都进行了持久化，集群模式为了防止数据丢失，需要将消息数据进行复制，副本因子即为消息数据的复制个数，类比hadoop。

- controller 控制器：类似于hadoop的hdfs中的namenode，负责消息副本和分区leader的管理，一套kafka集群启动后选举出一个broker节点作为controller。