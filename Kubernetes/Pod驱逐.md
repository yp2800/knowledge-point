# Pod 驱逐
## QoS
QoS （Quality of Service）服务质量，Kubernetes 使用 QoS 等级来确定何时调度和终结 Pod。
QoS 有三个等级

* Guaranteed 每个容器都必须有内存、CPU 限制和请求,而且必须是一样的。
* Burstable 该 Pod 不满足 QoS 等级的 Guaranteed 的要求。Pod 里至少有一个容器有内存或者 CPU 请求。
* BestEffort Pod 里的容器必须没有任何内存或者 CPU 的限制或请求。

## 驱逐信号
* memory.available	memory.available := node.status.capacity[memory] - node.stats.memory.workingSet
* nodefs.available	nodefs.available := node.stats.fs.available
* nodefs.inodesFree	nodefs.inodesFree := node.stats.fs.inodesFree
* imagefs.available	imagefs.available := node.stats.runtime.imagefs.available
* imagefs.inodesFree	imagefs.inodesFree := node.stats.runtime.imagefs.inodesFree

## 驱逐 pod 排名
* BestEffort 或 Burstable，其对短缺资源的使用超过其请求，些类 Pod 按优先级排序，然后使用高于请求。
* Guaranteed pod 和 Burstable Pod，其使用率低于请求，最后被驱逐。Guaranteed Pod 只有为所有的容器指定了要求和限制并且它们相等时才能得到保证。由于另一个 pod 的资源消耗，这些 Pod 保证永远不会被驱逐。如果系统守护进程（如 kubelet、docker、和 journald）消耗的资源多于通过 system-reserved 或 kube-reserved 分配保留的资源，并且该节点只有 Guaranteed 或 Burstable pod 使用少于剩余的请求，然后节点必须选择驱逐这样的 Pod 以保持节点的稳定性并限制意外消耗对其它 Pod 的影响。在这种情况下首先驱逐优先级最低的 Pod。

必要时，kubelet 会到遇到 DiskPressure 时驱逐一个 Pod 来回收磁盘空间。如果 kubelet 响应 inode 短缺，它会首先驱逐服务质量最低的 Pod 来回收 inodes。如果 kubelet 响应缺少可用磁盘，它会将 Pod 排在服务质量范围内，该服务会消耗大量的磁盘并首先结束这些磁盘。

## 节点 OOM 行为
如果节点 kubelet 回收内存之前经历了系统 OOM （内存不足）事件，它将基于 oom-killer 做出响应。
如果 kubelet 在节点经历系统 OOM 之前无法回收内存， oom-killer 将基于它在节点上使用的内存百分比算出一个 oom_score，并加上 oom_score_adj 得到容器的有效 oom_score，然后结束得分最高的容器。
预期的行为应该是拥有最低 service 质量并消耗和调度请求相关内存量最多的容器第一个被结束，以回收内存。

和 pod 驱逐不同，如果一个 pod 的容器是被 OOM 结束的，基于其 RestartPolicy，它可能会被 kubelet 重新启动。

## 软驱逐和硬驱逐区别
软驱逐有宽限时间，而硬驱逐是直接驱逐
软驱逐 
> 如果达到了软驱逐阈值，操作员可以指定从节点驱逐 pod 时，在宽限期内允许结束的 pod 的最大数量。如果指定了 pod.Spec.TerminationGracePeriodSeconds 值，kubelet将使用它和宽限期二者中较小的一个。如果没有指定，kubelet将立即终止 pod，而不会优雅结束它们。

* eviction-soft 描述了驱逐阈值的集合（例如 memory.available<1.5Gi），如果在宽限期之外满足条件将触发 pod 驱逐。
* eviction-soft-grace-period 描述了驱逐宽限期的集合（例如 memory.available=1m30s），对应于在驱逐 pod 前软驱逐阈值应该被控制的时长。
* eviction-max-pod-grace-period 描述了当满足软驱逐阈值并终止 pod 时允许的最大宽限期值（秒数）。

硬驱逐 硬驱逐阈值没有宽限期，一旦察觉，kubelet将立即采取行动回收关联的短缺资源。如果满足硬驱逐阈值，kubelet将立即结束 pod 而不是优雅终止


#### 参考
* [给 Pod 配置服务质量等级](https://k8smeetup.github.io/docs/tasks/configure-pod-container/quality-service-pod/)
* [配置资源不足时的处理方式](https://kubernetes.io/zh/docs/tasks/administer-cluster/out-of-resource/)