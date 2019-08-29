# Pod-nonroot
#### 权限有哪些分类
* root allow-privileged=true 允许修改系统文件 kubelet 启动参数配置
* root  allow-priledged=false 不允许修改系统文件
* 非 root 运行1 号进程 


#### pod 非 root 用户，开发进入容器排查问题， 如何非 root 用户启动?

> 使用 SecurityContext 进行配置，不过需要预置一些账户，这样可以从 nonroot su 到 root

*如果没有没有预置账户的话就不能切回 root 用户了*

切换 `su - root`时 `su: Cannot determine your user name.`

查看用户 `whoami` `whoami: cannot find name for user ID 2000`

----
注意：开发非 root 用户进入之后可以自己排查问题，如果是运维人员进去空口后需要 root 权限安装包调试呢，这里是安全和规范的一个平衡。

预留一个问题，如何去平衡呢？

