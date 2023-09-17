 <center>
     <h1>赵文敬</h1>
     <div>
         <span>
             <img src="assets/phone-solid.svg" width="18px">
             18395609209
         </span>
         ·
         <span>
             <img src="assets/envelope-solid.svg" width="18px">
             18395609209@163.com
         </span>
         ·
     </div>
 </center>


 ## <img src="assets/info-circle-solid.svg" width="30px"> 个人信息 

 - **性别：**男
 - **求职意向：**C++ 研发工程师
 - **工作经验：**1 年
 - **毕业院校：**哈尔滨工程大学
 - **专业：**仪器科学与技术
 - **工作经历：**
   - 腾讯科技(深圳)有限公司，IEG/光子游戏工作室群/欢乐游戏工作室，后台开发工程师，2022.5~至今

## <img src="assets/project-diagram-solid.svg" width="30px"> 项目经历

**工作项目：**

- **分布事务模块**

  **使用技术：**Kafka，Flink，Docker，Kubernetes，Istio，Helm，Bazel

  **功能概述：**项目用于为发货失败玩家进行补货，分为以下三个模块。
  
  - 事务接入API：当项目接入事务时生成总子账单分布式事务id，向Kafka中生产总账单，发货模块自动向Kafka中生产子账单。
  - 对账模块：Flink中拿到总账单和分帐单分别处理成两个数据流，通过CoGroup做对账逻辑，得到失败账单数据流，sink到Kafka。
  - 补偿模块：从Kafka消费失败账单，根据服务名选取不同补偿策略，发送邮件补偿玩家物品。

**在校项目：**

- **集群聊天服务器**

  **使用技术：**Muduo、Nginx、Redis、Mysql、Json、CMake、git

  **功能概述：**基于Muduo进行网络通信，基于nginx的tcp负载均衡实现聊天服务器的集群功能，基于redis中间件实现跨服务器通信，采用数据库连接池减小数据库连接开销，采用线程池减少线程创建开销，基于CMake构建项目集成编译环境和Shell一键编译脚本，git进行版本管理。

  - 服务端：

    1. 网络模块：基于Muduo进行网络通信，采用MultiReactor模式来提高网络并发量。

    2. 业务代码：解耦业务代码与网络代码。实现用户登录、注册、加好友、删好友、加群、退群、离线消息的功能。

    3. 池类算法：采用生产者消费者模式，有锁消息队列，条件变量实现线程池的动态扩容和缩减。
       - 数据库连接池：利用ORM封装解耦数据库代码与业务代码，利用智能指针的定制析构实现数据库连接自动归还连接池
       - 线程池：使用Linux pthread库实现线程池。
    4. 集群：
       - 负载均衡：利用Nginx反向代理服务器实现多服务器的负载均衡。
       - 消息中间件：利用Redis的发布订阅功能实现跨服务器通信。 

  - 客户端：利用qt开发UI界面，socket实现网络收发功能。 

- **分布式网络通信框架**

  **使用技术：**Muduo、Protobuf、Zookeeper、CMake、git

  **功能概述：**基于Muduo实现网络通信，基于Protobuf进行协议序列化和反序列化，基于Zookeeper实现分布式服务注册和服务发现，基于缓冲队列实现异步日志，基于CMake构建项目集成编译环境和设立了一键编译脚本，git进行版本管理。

  服务端：使用protobuf的service功能，并结合muduo实现服务的提供，利用zookeeper实现服务注册。

  客户端：阻塞调用远端方法，利用zookeeper做服务发现。

## <img src="assets/tools-solid.svg" width="30px"> 技能清单

- 熟悉C++继承与多态，内存模型，熟悉C++11中的类型转换、智能指针、bind和function、lambda表达式等的使用。熟练使用STL的序列式容器和关联式容器、了解相关容器的底层数据结构，了解SGI STL的空间配置策略。
- 熟悉Linux系统相关知识：如虚拟内存、进程、线程、进程间的通信、线程间同步、I/O多路复用（select、poll、epoll）等。了解死锁相关知识。
- 熟悉socket网络编程，熟悉TCP/IP协议栈相关知识，如TCP三次握手、四次挥手、重传机制、流量控制和拥塞控制。了解常见服务器网络模型。
- 熟悉栈、队列、数组、链表、树等常见的数据结构以及查找和排序算法。
- 熟悉SQL语句，熟悉InnoDB索引，熟悉事务及事务隔离级别，三大范式。
- 熟悉Kafka消息队列，使用场景，生产消费流程，选举策略，分区策略，数据存储形式，数据清理策略等。
- 了解Flink流批处理的使用
- 了解Kubernetes，Istio，Docker配置和使用
- 了解常见分布式事务模型，了解常见分布式一致算法，了解Zookeeper基本使用，了解常见负载均衡算法，了解RPC。

## <img src="assets/tools-solid.svg" width="30px"> 自我评价

通过github搜索研究代码。通过百度和stackoverflow搜索问题。通过B站，极客时间，博客，书本学习新知识。有较强的动手能力和自学能力，踏实肯干，遇到新问题能够尝试自己找方法解决。
