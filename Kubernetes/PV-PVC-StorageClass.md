# 三个概念 PV、PVC、StorageClass
* PV 持久化卷，支持本地存储和网络存储。
* StorageClass PV 的模板，可以动态的生成 PV。
* PVC Pod 配置 PVC，是对 PV 或者 StorageClass 的请求。

# 六个生命周期
* provisioning - 配置阶段，static (PV) 或者 dynamic (StorageClass)。
* Binding 绑定阶段，PVC 根据请求的条件筛选 PV，一量绑定后就排斥其它的绑定。
* Using 使用阶段。
* Releasing PVC 对象被删除后，就处于释放阶段。
* Reclaiming － 重声明阶段。
* Recycling － 回收阶段。

# 三种 PV 访问模式
* ReadWriteOnce：是最基本的方式，可读可写，但只支持被单个Pod挂载。
* ReadOnlyMany：可以以只读的方式被多个Pod挂载。
* ReadWriteMany：这种存储可以以读写的方式被多个Pod共享。

参考链接(https://zhuanlan.zhihu.com/p/29706309)