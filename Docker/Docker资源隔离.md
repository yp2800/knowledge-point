# Docker 资源隔离
## Cgroup CPU 资源隔离介绍
cgroup 中有三种 cpu 资源的限制方式

* cpuset 是以分配核心的方式进行隔离，资源分配的最小粒度是核心，隔离后运算的相互影响最低。
* cpuquota 比 cpuset 更细粒度的资源分配方式，并且保证 cgroup 使用 cpu 比率的上限，相当于 cpu 资源的硬限制。
* cpushares 是按权重比率分配 cpu 时间资源的方式。当 cpu 空闲的时候，某个要占用 cpu 的 cgroup 可以完全占用 cpu 时间，充分利用资源。而当其它的 cgroup 需要占用资源的时候，每个 cgroup都能保证基最低的占用时间比率，达到资源隔离的效果。

## Docker 中的 CPU 限制参数
* --cpu-shares CPU shares (relative weight)
* --cpu-period Limit CPU CFS (Completely Fair Scheduler) period
* --cpu-quota Limit CPU CFS (Completely Fair Scheduler) quota
* --cpuset-cpus CPUs in which to allow execution (0-3, 0,1)

--cpu-shares 对应用于 cgroup;

--cpu-period 和 --cpu-quota 是 docker 1.7 版本引入的 两个参数控制容器可以分配到的CPU时钟周期;

--cpu-set-cpus 指定窗口使用的核心;

##### 参考
* [Cgroup的CPU资源隔离介绍&docker cpu限制](https://blog.csdn.net/liukuan73/article/details/53358423)