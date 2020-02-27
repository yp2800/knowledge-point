# k8s 容器内查看 cpu、memory分配情况
**/sys/fs/cgroup**信息在此目录下

## cpu 分配情况
1. 限下限

   cpu.shares    
2. 限上限

   cpu.cfs_period_us      
   cpu.cfs_quota_us      
3. 限实时任务上限

   cpu.rt_period_us      
   cpu.rt_runtime_us 

cpu request 为 cpu.shares
cpu limit 为 cpu.cfs_quota_us / cpu.cfs_preiod_us

## memory 分配情况

1. memory.usage_in_bytes  当前使用
2. memory.limit_in_bytes  memory limit 设置
3. memory.oom_control
4. memory.soft_limit_in_bytes

####参考资料
[digoal/blog](https://github.com/digoal/blog/blob/master/201606/20160613_01.md)

[限制cgroup的内存使用](https://segmentfault.com/a/1190000008125359)

[容器中的JVM资源该如何被安全的限制](https://qingmu.io/2018/12/17/How-to-securely-limit-JVM-resources-in-a-container/#OpenJ9)