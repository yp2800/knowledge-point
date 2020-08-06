# Kubernets 部署策略
* 重建： 停止旧的版本，部署新版本
* 滚动更新：一个接一个地以滚动更新方式发布新版本
* 蓝绿部署: 新版本和旧版本一起存在，然后切换流量
* 金丝雀： 将新版本一部分面向用户发布，然后继续全量发布
* A/B测：以精准的方式（HTTP 头，cookie, 权重等）。Kubernetes 原生并不支持，需要额外的一些高级组件来完成改设置（比如 Istio、Linkerd、Traefik、或者自定义 Nginx/Haproxy等）


#### 参考
* [Kubernetes 部署策略详解](https://zhuanlan.zhihu.com/p/55964678)