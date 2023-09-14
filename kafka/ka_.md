

**Controller：**kafka启动时，会在所有的broker中选择一个controller

- 创建topic，添加partition，修改replications数量之类的管理任务都是由controller完成的。kafka分区leader的选举也是由controller决定的

- **Broker选举：**
  1. kafka集群启动时，每个broker都会尝试取Zookeeper上注册成为Controller（ZK临时节点），有一个竞争成为Controller之后，其余broker会注册该节点的监视器。
  2. 一旦临时节点状态发生变化，进行相应处理。如果Controller崩溃，其余broker重新竞选为Controller。

**Partion副本Leader选举：**

- partion leader选举由controller决定，controller会将leader的改变直接通过rpc的方式通知需要为此做出相应的Broker

- **选举规则：**

  1. controller读取当前分区的ISR，选择其中一个作为Leader。
  2. 没有则选择一个存活的replicas作为Leader。
  3. 所有replicas都宕机了，则新的leader为-1。

- **为什么不用ZK来选举Partiton的Leader：**ZK选举速度满。如果kafka一个broker上可能有多个leader，一旦崩溃，需要有多个leader选举，对ZK的压力比较大，可以通过ISR可以快速选举。

- **Leader负载均衡：**某个broker崩溃之后，partition副本的leader通过controller重新选举，会导致leader在broker上分布不均匀。通过下面的指令可以把partion副本leader分配到系统优先选择的broker上，确保leader是均匀分配的。

  ```
  bin/kafka-leader-election.sh
  ```

## 生产消费流程

**生产者生产流程：**

1. 通过zookeeper找leader partition所在的broker。

   - `“/brokers/topics/主题名/partitions/分区名/state”`节点里面的数据的leader字段，找到该partition的leader所在的brokerid。

   - `brokers/ids`可以查到broker的host和端口号

2. 发送消息broker，leader partion将数据写入本地log中。

3. 其余follower拉取并写入日志文件，返回ACK给Leader。

4. 接收到所有ISR中replica的ACK后，返回ACK给生产者。

**消费者消费流程：**通过offset来确定自己可以消费的数据范围，并通过提交offset来跟踪消费进度，保证了单partition消费的有序性和容错性。

1. 通过Zookeeper找leader partition所在broker
2. 通过Zookeeper找消费者partition对应的offset。
3. 从offset往后顺序拉取数据
4. 提交offset（自动提交--每隔多少秒提交一次offset，手动提交--放入到事务中提交）

## Kafka数据存储形式

