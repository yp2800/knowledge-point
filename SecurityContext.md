# SecurityContext
SecurityContext 是为了限制不可信容器的行为，保护系统和其它的容器不受影响

### SecurityContext 有三种配置方式
* Container-level Security Context：仅应用到指定的容器
* Pod-level Security Context: 应用到 Pod 内所有窗口以及 Volumn
* Pod Security Policies (PSP)： 应用到集群内部所有的 Pod 以及 Volumn

### 例子
* Pod-level

```
pods/security/security-context.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
  - name: sec-ctx-vol
    emptyDir: {}
  containers:
  - name: sec-ctx-demo
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    volumeMounts:
    - name: sec-ctx-vol
      mountPath: /data/demo
    securityContext:
      allowPrivilegeEscalation: false
```
容器里是非 root 用户运行 `id` `uid=1000 gid=3000 groups=2000`

* Container-level

```
pods/security/security-context-2.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-2
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo-2
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
```
容器里是非 root 用户运行 `id` `uid=2000 gid=0(root) groups=0(root)`


* Linux capabilities 可以继承部分权限

> 为了执行权限检查，传统UNIX
实现区分了两类流程:特权流程
进程(其有效用户ID为0，称为超级用户或
和非特权进程(其有效UID非零)。
同时，特权进程绕过所有内核权限检查
非特权进程必须基于完全权限检查
进程的凭据(通常为:有效UID、有效GID、
及补充团体名单)。

> 从内核2.2开始，Linux传统上划分特权
将超级用户关联到不同的单位，称为功能，
可以独立启用和禁用。能力是一个
线程属性。

```
pods/security/security-context-4.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-4
spec:
  containers:
  - name: sec-ctx-4
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

参考链接(https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)