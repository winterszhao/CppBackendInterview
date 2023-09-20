# Flink简介

jdk：

```shell
# 安装jdk，默认路径/usr/lib/jvm
yum install java-1.8.0-openjdk.x86_64
yum install java-devel

# java环境设置，vi /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.382.b05-1.el7_9.x86_64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

maven:

```shell
sudo mv -f apache-maven-3.9.4 /usr/local/

# maven环境设置，sudo vim /etc/profile
export MAVEN_HOME=/usr/local/apache-maven-3.9.4
export PATH=${PATH}:${MAVEN_HOME}/bin
```

```shell
source /etc/profile	# 使环境变量生效
mvn -v
```

使用mven创建项目

```shell
mvn archetype:generate 
	-DgroupId=com.example 				# 指定项目的Group ID（组织标识）
	-DartifactId=my-app 				# 指定项目的Artifact ID（项目标识）
	-DarchetypeArtifactId=maven-archetype-quickstart # 指定用于生成项目的原型
	-DinteractiveMode=false				# 禁用交互模式
```



## **Flink**

**是什么？**基于数据流有状态计算

- 数据流：实时逐条处理无限数据流
- 有状态：保存每个算子计算的中间结果

![](C:\Users\winterszhao\Desktop\面试\flink\flink-home-graphic.png)

- **编程模型：**source（数据输入）-》transformation（数据计算）-》sink（数据输出）

  API：SQL/Table API -》DataStream API（流批处理API） -》Stateful Event-Driven Applications（对底层关键计数的操纵，Event，state，time，window）

- **Flink流处理特性：**

  1. 支持**高吞吐，低延迟，高性能**的流处理
  2. 支持带有事件时间的**窗口（Window）**操作
  3. 支持有状态计算的**Exactly-once**语义
  4. 支持高度灵活的窗口（Window）操作，支持基于**time，count，session，以及data-drivern**的窗口操作。
  5. 支持具有**Backpressure**功能的持续流模型
  6. 支持基于轻量级**分布式快照（Snapshot）**实现的容错
  7. 一个运行时同时支持**Batch On Streaming**处理和**Streaming**处理
  8. Flink在JVM内部实现了自己的**内存管理**
  9. 支持迭代计算
  10. 支持程序自动优化：避免特定情况下Shuffle，排序等昂贵操作，中间结果有必要进行缓存。

## 架构

Flink是主从架构

- 主节点JobManager：做集群管理工作，资源管理，任务调度，Checkpoint（容错），心跳检测等
- 从节点TaskManager：负责具体的任务执行，心跳汇报等。
  - Slot：槽，在Standalone槽固定数量，Yarn

**集群搭建：**

- Local模式（本地模式）：一个进程模拟全部的角色，处理所有的代码流程
- Standalone模式（独立模式）：主从节点独立进程。
- Yarn模式：基于Yarn来运行Flink，不需要额外待见，只需要把Yarn，HDFS启动即可。

## 流式计算

|          | 批量计算                                                     | 流式计算                                                     |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 处理方式 | 批量处理（一批数据为单位进行处理）<br>全量计算（对整个数据集进行计算和处理） | 逐条处理（来一条处理一条，不是处理整个数据集）<br>增量计算（对每个数据进行增量处理，而不是计算整个数据集） |
| 时效性   | 延迟处理（数据会被收集一段时间，形成一个批次后再进行处理）   | 实时性（数据到达立即进行处理）                               |
| 输入数据 | 有限数据集（有界，数据集可以是一段时间内的数据）             | 无限数据流（kafka等连续接收数据）                            |
|          | 数据一致性（完整性：确保所有数据都会被处理，不漏数据。去重性：避免重复处理同一数据，保证结果的准确性） | 状态管理（计算中间结果，维护状态信息来跟踪数据处理过程）     |
|          | 离线环境（通常在离线环境中进行，即数据已经被收集完毕后进行处理。可以在较为稳定的环境中进行，不需要实时响应和即时处理） | 可扩展性（水平扩展，通过并行提高吞吐量和处理能力）           |
| 使用场景 | 对历史数据的处理，对时效性要求不高。 <br>但是对时效性要求高的场景并不适用（实时监控网站一场情况，实时监控道路拥堵情况，实时监控全国疫情爆发情况，事实监控网站成交情况） |                                                              |

## Flink开发

1. **Flink分层API**
   - SQL/Table API（最顶层）StreamTableEnvironment
   - DataStream API（中间层）StreamExecutionEnvironment
   - Stateful Function（最底层）
2. **Flink程序开发流程**
   1. 构建流式执行环境
   2. 数据输入
   3. 数据处理
   4. 数据输出
   5. 启动流式
3. **需求：**使用Flink程序，进行Wordcount单词统计