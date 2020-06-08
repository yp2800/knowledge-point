# Kubernetes 扩展点
Kubernetes 是高度可配置和可扩展的，以实现自己想要的功能。定制方法大概分为两个方面`配置`和`扩展`

* 配置只涉及更改标志参数、本地配置文件或者 API 资源;
* 扩展涉及运行额外的程序或服务;

本文只讨论第二部分`扩展`
## Kubernetes 有哪些扩展点？
* kubectl 插件
* apiserver 处理所有请求。
* apiserver 提供各种资源
* Kubernetes 调度器
* Kubernetes 控制器
* Kubelet 网络插件扩展
* Kubelet 卷，即存储插件

引用官方的图，有哪些扩展点，该决定在哪里扩展添加功能。
扩展点的图
![扩展点](https://docs.google.com/drawings/d/e/2PACX-1vSH5ZWUO2jH9f34YHenhnCd14baEb4vT-pzfxeFC7NzdNqRDgdz4DDAVqArtH4onOGqh0bhwMX0zGBb/pub?w=425&h=809)

![流程图](https://docs.google.com/drawings/d/e/2PACX-1vRWXNNIVWFDqzDY0CsKZJY3AR8sDeFDXItdc5awYxVH8s0OLherMlEPVUpxPIB1CSUu7GPk7B2fEnzM/pub?w=1440&h=1080)
#### 参考 
* [扩展 Kubernetes 集群](https://kubernetes.io/zh/docs/concepts/extend-kubernetes/extend-cluster/)