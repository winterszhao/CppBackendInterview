## kafka应用场景

1. **异步处理：**
   - **问题：**比较耗时的操作会阻塞用户回包，
   - **解决：**将耗时操作放在其他系统中，通过消息队列将需要进行处理的消息进行存储，其他系统可以消费消息队列中的数据。
   - 比较常见的耗时操作：发送短信验证码、发送邮件
2. **系统解耦：**
   - 原先一个微服务是通过接口调用另一个服务，这时候耦合很严重，只要接口发生变化就会导致系统不可用。
   - 使用消息队列可以将系统进行解耦合，第一个微服务可以将消息放入到消息队列中，另一个微服务可以从消息队列中把消息取出来进行处理，进行系统解耦。
3. **流量削峰：**
   - 因为消息队列时低延迟、高可靠、高吞吐的，可以应对高并发
4. **日志处理：**
   - 可以使用消息队列作为临时存储，或者一种通信管道

## 消息队列两种模型

生产者，消费者模型

1. **点对点模式：**一个生产者对应一个消费者，消费之后删除
2. **发布订阅模式：**一对多，一个消息可以可以被多个订阅者消费

## kafka

1. **Publish and subscribe：** 发布和订阅流数据流，类似于消息队列
2. **store：**以容错的持久化方式存储数据流？？？
3. **Process：**处理数据流（kafka stream类似于flink??）

通常把kafka用**在两类程序：**

1. 建立**实时数据管道**，用来可靠的在系统或应用程序之间获取数据。
2. 构建**实时流应用程序**，以转化或响应数据流。

​	![](C:\Users\winterszhao\Desktop\面试\kafka\图片1.png)

1. **Producers：**生产者，应用程序投递消息
2. **Consumers：**消费者，应用程序消费消息。
3. **Connectors：**kafka连接器可以把数据空中的数据导入到kafka，也可以把kafka的数据导出到数据库中。
4. **Stream Processers：**流处理器可以kafka中拉取数据，也可以将数据写入到kafka中。

## Kafka部署

[k8s部署](https://blog.51cto.com/binghe001/5245687)

## 基础操作

**Topic：**必须先创建topic，生产者生产消息到指定的topic中，消费者消费指定topic的数据。

```shell
cd opt/kafka/bin
# 增加topic
kafka-topics.sh --zookeeper [zookeeper cluster ip]:2181 --create --topic winters-1 --partitions 1 --replication-factor 1
# 删除topic
kafka-topics.sh --zookeeper [zookeeper cluster ip]:2181 --delete --topic winters-1
# 测试生产者
# 测试消费者
```

**Kafka tools操作kafka：**一个kafka的客户端，浏览zookeeper的数据

- Brokers：部署的kafka个数
- Topics：kafka主题，__consumer_offsets
- Consumers：消费者

**基准测试（benchmark testing）：**是一种测量和评估软件性能指标的活动。我们可以通过基准测试，了解到软件、硬件的性能水平。主要测试负载的执行时间、传输速度、吞吐量、资源占用率等。生产5000W

**单分区单副本：**分区partitions是什么？？？差不多一秒10多万条，10多M







## Kafka概念

**broker：**单个kafka进程

**topic：**一个topic有多个分区（怎么保证读出的数据有序），和多个副本

- **partition分区：**数据分散存储，分区，将数据分在不同的机器上，解决单台机器IO瓶颈。
- **replication副本：**数据备份，容错，确保某台机器出现故障时，数据仍然可用（partition的copy）。
- **offset偏移量：**记录消费者消费到哪里了，拉模式，确保消费者宕机重启后还能从offset继续消费

**consumer group消费者组：**消费者分组内，一个分区只能对应一个消费者来消费。n个分区最多n个消费者，多出的消费者消费不到数据。多个分区一个消费者？？？

**生产者幂等性和事务：**一次生产和多次生产操作的结果是一致的。