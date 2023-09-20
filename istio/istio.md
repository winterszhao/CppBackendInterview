## isito核心功能

istio安装时需要看对k8s版本的支持情况，用的k8s版本1.17，isito版本1.11

## istio架构

![arch](pic\arch.svg)

**Enovy：**数据平面，作为服务的sidecar，代理服务的所有进出口流量。

1. 动态服务发现：
2. 负载均衡
3. tls 终端：外部流量到enovy这一层做https的认证，envoy到服务不需要认证
4. http和grpc的proxy代理
5. 断路保护
6. 健康检查
7. 阶段发布（百分比流量）
8. 故障注入
9. 丰富的指标

**Pilot：**服务发现，提供服务发现功能和路由规则。

- **服务发现：**k8s服务发现，自动检测集群中的service和service的endpoints（多个pod），大多数服务都是通过 [Pilot](https://istio.io/zh/docs/reference/glossary/#pilot) adapter 自动加入到服务注册表里的。

- **服务注册中心：**istio维护了一个内部服务注册表 (service registry)，包含网格中运行的服务和服务的endpoints，istio使用服务注册表生成envoy配置。

- **virtual service：**

  大多数基于微服务的应用程序， 每个服务的工作负载都有多个实例来处理流量，称为负载均衡池。默认情况下， Envoy 代理基于轮询调度模型在服务的负载均衡池内分发流量，按顺序将请求发送给池中每个成员， 一旦所有服务实例均接收过一次请求后，就重新回到第一个池成员。

  **虚拟服务：**包含一组路由规则，将符合条件的请求匹配到虚拟服务指定的实例上。

  ```yaml
  spec:
    hosts:
    - hlmjusb3.hlmj3dout.svc.cluster.local	# 主机名
    http:
    # 从上到下匹配
    # 请求头isuindebug字段为1，且uri前缀为'/'
    - match:		# 匹配规则
      - headers:
          isuindebug:
            exact: "1"
        uri:
          prefix: /
      route:		# 转发的目标
      - destination:
          host: hlmjusb3.hlmj3dout.svc.cluster.local	# 服务主机名
          port:	# 端口号
            number: 50051	
          subset: prerelease	# 定义的子集名
        weight: 100
    # 请求头routekey字段为以0为结尾的数字，且uri前缀为'/'
    - match:
      - headers:
          routekey:
            regex: '[0-9]*0$'
        uri:
          prefix: /
      route:
      - destination:
          host: hlmjusb3.hlmj3dout.svc.cluster.local
          port:
            number: 50051
          subset: online
        weight: 100
      - destination:
          host: hlmjusb3.hlmj3dout.svc.cluster.local
          port:
            number: 50051
          subset: canary
        weight: 0
    # 其余请求
    - match:
      - uri:
          prefix: /
      route:
      - destination:
          host: hlmjusb3.hlmj3dout.svc.cluster.local
          port:
            number: 50051
          subset: online
        weight: 100
  ```
  
  路由规则destinationRule：定义了路由的目标，和目标的负载均衡策略
  
  ```yaml
  spec:
    exportTo:	# 导出到所有命名空间
    - '*'
    
    host: hlmjusb3.hlmj3dout.svc.cluster.local	# 服务主机名
    
    subsets:	# 定义服务的子集
    # online子集：标签isCanary为false的服务实例
    - labels:
        isCanary: "false"
      name: online
    # canary子集：标签isCanary为false的服务实例 
    - labels:
        isCanary: "true"
      name: canary
    # prerelease子集：标签isPreRelease为true的服务实例 
    - labels:
        isPreRelease: "true"
      name: prerelease
      
    trafficPolicy:	# 路由规则，每个子集可以单独写
      loadBalancer:
        consistentHash:	# 一致性哈希来负载均衡
          httpHeaderName: routekey
          minimumRingSize: 1024
      tls:
        mode: DISABLE
  ```
  
- **服务条目：**外部服务引入网格内部，像内部服务一样管理，定义一个资源

- **网络可靠性：**VS的配置

  1. **超时：**envoy代理等待来自给定服务答复的时间量

     ```
     http:
     - route:
       timeout: 10s
     ```

  2. **重试：**重试多少次，不再重试

     ```
     http:
     - route:
       retries:
       	attempts: 3
       	perTryTimeout: 2s
     
     ```

  3. **断路器：**

     ```yaml
     
     ```

**Galley：**配置，负责sidecar注入等工作

**Citadel：**证书生成，起到安全作用，比如：服务跟服务通信的加密

luckylotteryV2外网发布，支付组件，openid转换工具外网发布，封禁发货dbassist发货校验开发和bug修改

luckylottery监控，订阅活动bug排查，idip代码调整



服务 -> sidecar -> 
