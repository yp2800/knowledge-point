# kube-proxy 原理
## Kubernetes 负载均衡
* 四层负载 service，由 kube-proxy 实现。ClusterIP 不会因为 Pod 状态改变而改变。需要注意的是 VIP即 ClusterIP是个假的 IP，这个 IP 在整个集群中根本不存在，当然也就无法通过 IP 协议栈无法路由，底层underlay 设备更无法感知这个 IP 的存在，因此 ClusterIP 只能是单主机（Host Only）作用域可见，这个 IP 在其它节点以及集群外均无法访问。
* 七层负载 ingress

## 为什么集群内所有节点都能访问 Service 呢？
kube-proxy默认会在所有的 Node 节点都创建这个VIP并且实现负载，所以在部署 Kubernetes 后发现 kube-proxy 是一个 DaemonSet。

Kubernetes 采用的所以网络模型，都满足下面三个条件

1. 容器之间要求不需要任何 NAT 能直接通信;
2. 容器与 Node 之间要求不需要任何 NAT 能直接通信; 
3. 容器看到自身的 IP 和外面看到它的 IP 必须是一样的，即不存在 IP 转化的问题。

kube-proxy 默认会优先选择基于内核态的负载作为后端实现机制，目前 kube-proxy 默认是通过 iptables实现负载的，在此之前还有一种为 userspace 模式，其实也是基于 iptables 实现，可以认为当前的 iptables模式是对之前 userspace模式的优化。

## ClusterIP DNAT SNAT
在 node 上查看 nat 规则`iptables-save -t nat | grep -- '-A OUTPUT'` 和`iptables-save -t nat | grep -- '-A POSTROUTING'` 发现
访问 pod cluserIP，是从 node 节点上做了 DNAT,然后在发出包的时候又做了 SNAT。

## NodePort
从集群外访问 Node:Port `iptables-save -t nat | grep -- '-A PREROUTING'` 
`iptables-save -t filter | grep -- '-A FORWARD'`

## kube-proxy 也可以基于 IPVS 原理实现
IPVS 有三种模式 
1. NAT 模式 （kube-proxy 使用此种模式）
2. GATEWAY 模式
3. IPIP 隧道模式
IPVS是专门设计用来做内核态四层负载均衡的，性能更好，应用越来越广泛，IPVS必然未来会取代 iptables 成为 Kubernetes 成为 Kubernetes Service 的默认实现后端。
#### 参考
* [IPVS从入门到精通kube-proxy实现原理](http://dockone.io/article/9441)