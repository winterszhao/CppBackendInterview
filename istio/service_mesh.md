## 服务治理要求

1. **注册中心：**当我们服务节点数几十上百的时候，需要对服务有动态的感知
2. **链路追踪：**链路当服务链路调用很长的时候如何实现链路的监控
3. **熔断降级限流：**单个服务的异常，如何能避免整条链路的异常（雪崩），需要考虑熔断、降级、限流
4. **负载均衡：**服务高可用

典型框架比如有：Dubbo,默认采用的是Zookeeper作为注册中心。

## CNCF

- **Jaeger：**分布式链路追踪

  **kiali：**istio监控平台，通过jaeger提供分布式链路追踪功能
- **flux:** gitops

## Istio

优缺点：

- 优点：

1. 屏蔽分布式系统通信的复杂性（负载均衡、服务发现、认证授权、监控追踪、流量控制等），服务只用关注业务逻辑。
2. 对应用透明
3. 应用语言无关，代码无侵入

- 缺点：

1. 代理模式计算并转发请求，一定程度降低系统性能，增加系统资源消耗
2. service 



1. 自动注入：sidecar-injector：会修改应用程序的描述信息，注入envoy

2. 流量拦截：pod创建时会设置iptable路由规则，iptables对数据进行检查和访问控制，路由到envoy

   数据包 -> iptables -> 代理容器 -> 容器

3. 服务发现：envoy通过Pilot获取服务信息和负载均衡策略，选择对应的服务实例。

   ```yaml
   # http请求
   http:
   - match: # 匹配
   	- header: # 头部
   		cookie:
   			exact: "group=dev"	# cookie中包含group=dev则流量转发到v2, 否则转发到v1
   	route: # 路由
   	- destination:
   		name: v2
   	- route:
   		- destination:
   			name: v1
   ```

4. 流量治理：envoy同构Pilot设置的流量规则（一小时3次），确定是否放行流量

5. 访问安全：citadel保存服务的证书和密钥

6. 服务监控：envoy上报给mixer进出口流量

7. 策略执行：mixer判断请求是否能放行







  

  

  

  

  

  

  

  

  ```
  解决idip cgi配置加载失败的问题，排查proxy连接gamesvr不通的问题
  ```

  