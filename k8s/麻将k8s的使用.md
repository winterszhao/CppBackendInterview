## Pod

**生命期：**

1. **init容器：**istio-init

2. **主容器：**

   1. **istio-proxy：**服务代理的sidecar

      postStart：  `pilot-agent wait`

      readinessProbe: 30秒一次本机:15021/healthz/ready的http请求

   2. **configagent：**同步七彩石配置的

      postStart：`/bin/sh -c while [ \"$(curl --silent  curl 127.0.0.1:12580/ready|xargs)\" != \"1\" ]; do sleep 1; done`

   3. **服务的主容器：**

      DEBUG_START看一下容器的dokcerfile是不是有个while(1)给挂起了
   
3. **重启策略：**always间隔重启

4. **调度：**

```yaml
# k8s-app值为服务名
topologyKey: failure-domain.beta.kubernetes.io/zone	# 按可用区进行调度，反亲和k8s-app:服务名的节点
weight: 10
topologyKey: kubernetes.io/hostname	# 按Node进行调度，反亲和pod所在node
weight: 90
```

5. **数据存储：**

   ```
   # hlsvr
   # hostPath: 主机文件
   	# core文件目录：corefiles: /data/corefile/
   	# log文件目录：logpath: /data/log/
   
   # secret:
   	# default-token-qt9n8	#
   	# configagent-etcd-secret
   # configMap
   	# transformconfig
   	# rainbowconfig
   # downwardAPI: # 将一些元数据注入容器中
   	# podinfo
   		# 文件rainbowinfo.json从pod字段metadata.annotations['happygame/rainbowinfo']
   		# 文件instanceinfo.json从pod字段metadata.annotations['happygame/instanceinfo']
   		# 文件namespace从pod字段metadata.namespace
   		# 文件appname从pod字段metadata.labels['app']
   # emptyDir:	
   	# happygame-share-volume
   
   # configagent注入
   	# 文件rainbowinfo.json从字段metadata.annotations['happygame/rainbowinfo']
   	# instanceinfo.json文件从字段metadata.annotations['happygame/instanceinfo']
   	# namespace文件从字段metadata.namespace
   	# appname文件从字段metadata.labels['app']
   ```

   

## Pod控制器

deployment更新策略：

```yaml
    strategy:
      rollingUpdate:
        maxSurge: 1
        maxUnavailable: 0
      type: RollingUpdate
```

有个ds，balance-tool：

```
nodeSelector选择了dedicated: gamesvr标签的node
NoSchedule容忍了dedicated：gamesvr标签node的NoSchedule污点
```

## Service

没找到kube-proxy的cm，ipvsadm命令没用，估计不是使用的ipvs
