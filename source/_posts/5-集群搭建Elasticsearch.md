---
title: 5 集群搭建Elasticsearch
date: 2019-05-29 15:01:15
categories: 
- Elasticsearch专题
tags:
- es部署
---
### 1 集群搭建

同样是每台主机都需要安装，过程很简单。

这时我们只是每台主机都安装好了 Elasticsearch，接下来我们还需要将它们联系在一起构成一个集群。
安装完之后，Elasticsearch 的配置文件是 /etc/elasticsearch/elasticsearch.yml，接下来让我们编辑一下配置文件：
<!--more-->

* **集群的名称**
通过  $\color{red}{cluster.name}$可以配置集群的名称，
集群是一个整体，因此名称都要一致，所有主机都配置成相同的名称，配置示例：
```json
cluster.name: germey-es-clusters
```

* **节点的名称**
通过  $\color{red}{node.name}$可以配置每个节点的名称，每个节点都是集群的一部分，每个节点名称都不要相同，可以按照顺序编号，配置示例：
```json
node.name: es-node-1
```
其他的主机可以配置为 es-node-2、es-node-3 等。


* **是否有资格成为主节点**
通过  $\color{red}{node.master}$可以配置该节点是否有资格成为主节点，如果配置为 true，则主机有资格成为主节点，配置为 false 则主机就不会成为主节点，可以去当数据节点或负载均衡节点。注意这里是有资格成为主节点，不是一定会成为主节点，主节点需要集群经过选举产生。这里我配置所有主机都可以成为主节点，因此都配置为 true，配置示例：

```json
node.master: true
```


* **是否是数据节点**
通过 $\color{red}{node.data}$ 可以配置该节点是否为数据节点，如果配置为 true，则主机就会作为数据节点，注意主节点也可以作为数据节点，当 $\color{red}{node.maste}$r 和 $\color{red}{node.data}$ 均为 false，则该主机会作为负载均衡节点。这里我配置所有主机都是数据节点，因此都配置为 true，配置示例：
```json
node.data: true
```


* **数据和日志路径**
通过  $\color{red}{path.data}$ 和 $\color{red}{path.logs }$ 可以配置 Elasticsearch 的数据存储路径和日志存储路径，可以指定任意位置，这里我指定存储到 1T 硬盘对应的路径下，另外注意一下写入权限问题，配置示例：
```json
path.data: /datadrive/elasticsearch/data
path.logs: /datadrive/elasticsearch/logs
```


* **设置访问的地址和端口**
我们需要设定 Elasticsearch 运行绑定的 Host，默认是无法公开访问的，如果设置为主机的公网 IP 或 $\color{red}{0.0.0.0}$  就是可以公开访问的，这里我们可以都设置为公开访问或者部分主机公开访问，如果是公开访问就配置为：
```
network.host: 0.0.0.0
```


* **集群地址设置**
通过 $\color{red}{discovery.zen.ping.unicast.hosts}$可以配置集群的主机地址，配置之后集群的主机之间可以自动发现，这里我配置的是内网地址，配置示例：
```
discovery.zen.ping.unicast.hosts: ["10.0.0.4", "10.0.0.5", "10.0.0.6"]
```

* **节点数目相关配置**
为了防止集群发生“脑裂”，即一个集群分裂成多个，通常需要配置集群最少主节点数目，通常为 (可成为主节点的主机数目 / 2) + 1，例如我这边可以成为主节点的主机数目为 7，那么结果就是 4，配置示例：
```json
discovery.zen.minimum_master_nodes: 2
```

另外还可以配置当最少几个节点回复之后，集群就正常工作，这里我设置为 4，可以酌情修改，配置示例：
```json
gateway.recover_after_nodes: 4
```

**接下来启动 es 
所有主机都启动之后，我们在任意主机上就可以查看到集群状态了，命令行如下：**
```json
curl -XGET 'http://localhost:9200/_cluster/state?pretty'
```

### 2 异常处理

**报错：**
```json
ERROR: bootstrap checks failed
system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk
```
**解决：**
这是在因为Centos6不支持SecComp，而ES5.2.0默认bootstrap.system_call_filter为true进行检测，所以导致检测失败，失败后直接导致ES不能启动。解决：
在elasticsearch.yml中配置bootstrap.system_call_filter为false，注意要在Memory下面:
bootstrap.memory_lock: false
bootstrap.system_call_filter: false




**报错：**
```json
elasticsearch process likely too low, increase to at least [65536]
```
**解决:** 修改切换到root用户修改配置limits.conf 添加下面两行命令:vi /etc/security/limits.conf

* soft nofile 65536
* hard nofile 65536
* soft nproc 2048
* hard nproc 4096
如果是root用户需要注销一下




**报错：**
```json
virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```
**解决：**
在   /etc/sysctl.conf文件最后添加一行
vm.max_map_count=262144
并执行命令：
sysctl -p



**报错:** 
```json
clusterStateVersion=-1}]], but needed [2]), pinging again
```
**解决 :** 
因为9300端口没有开放
如果想开放端口（如：9300）
（1）通过vi /etc/sysconfig/iptables 进入编辑增添一条-A INPUT -p tcp -m tcp --dport 8889 -j ACCEPT 即可
（2）执行 /etc/init.d/iptables restart 命令将iptables服务重启#
（3）保存 /etc/rc.d/init.d/iptables save