# Docker
关于 Docker 从 1.3之后很少关注新的特性了，无意中发现 `multi-stage builds` 多阶段构建。

* 多阶段构建 （17版本引入）
* 支持使用 SSH 进行远程连接 （v18.09引入）
* BuildKit
* 实验特性

## 什么是多阶段构建？
比如 java 应用在公司的 ci 流程中，一般都是先起来一个容器去构建出 war 包或者 jar 包，之后再用 dockerfile build 出去一个运行时的镜像。那现在这个过程可以被 docker 自身简化了。这也就是为什么`multi-stage builds`出现了。

下面来看一些例子来了解（这里以官方出的例子）

**注**例子里的 app.go 来自 `https://github.com/alexellis/href-counter`

* Use multi-stage builds

`Dockerfile` 文件内容如下，构建命令`docker build -t alexellis2/href-counter:latest .`， COPY --from=0 拷备从第一段构建出的 target

```
FROM golang:1.7.3
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=0 /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]  
```

* Name your build stages

第一阶段的构建使用`AS`关键字创建别名，第二阶段 COPY --from=别名引用第一阶段构建出的 target。

```
FROM golang:1.7.3 AS builder
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go    .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]
```

* Stop at a specific build stage

可以只构建第一阶段 `docker build --target builder -t alexellis2/href-counter:latest .` 用于 debug

* Use an external image as a "stage"

COPY --from=image 可以来其它的镜像里的文件 `COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf`

## 可使用 .dockerignore 忽略不需要的文件
.dockerignore 文件名是固定的，并且需要放到 Dockerfile 的根目录下。最后 Docker CLI 会从 build context 中先过滤掉 .dockerignore 忽略的内容，再将剩余的内容传送到 Docker Daemon，可以看出 .dockerignore 的作用是减轻 Docker CLI 和 Docker Daemon 的压力。

## 支持使用 SSH 进行远程连接
客户端和引擎之间的连接方法允许简单的、共享的 ssh配置，这比之前的自定义 CA证书解决的方案更加常见和容易理解。` docker -H ssh://vagrant@192.168.1113 run -ti ubuntu echo “hello”` **需要配置 ssh 密钥登陆**

也可以配置环境变量 `export DOCKER_HOST=ssh://root@192.168.1.113` 再执行 docker 命令，也是去其它机器上去执行了。

ssh 代理

## BuildKit
docker 19 版本已经正式支持 BuildKit，但是默认没有开启，可以通过设置环境变量DOCKER_BUILDKIT=1启用BuildKit功能。也可以通过配置 docker daemon 参数 `/etc/docker/daemon.json` 如下配置

```
{
  "features": {
    "buildkit": true
  }
}
```

虽然 BuildKit 被 docker集成，但并不依赖 docker ,BuildKit 可以作为单独的组件运行，只需要配合 registry及 runc 或者 containerd就可以构建镜像。

BuildKit最大的好处在于可以实现并行构建, 在支持Dockerfile Multi-Stage功能后, 这项功能会极大提高构建效率.

**LLB** 是一个中间语言, 类似于LLVM中的LLVM IR. 开发人员可以通过这套中间语言去优化构建流程, 而不需要对上层描述语言如Dockerfile进行修改

## 实验特性
现在程序语言大多都有软件包管理, 在构建时, 常常会首先下载依赖包, 如果重复构建, 会每次都重新下载, 非常麻烦.

在BuildKit中为RUN指令新增了一个缓存选项: –mount=type=cache, 可以指定缓存目录, 将依赖包下载到缓存目录中, 每次构建都使用同样的cache, 只要依赖没有变化, 就不需要重新下载软件包.
除了–mount=type=cache外, 还支持其他几个选项:

+ mount=type=bind: 可以将上一个构建阶段的目录挂载到这次构建过程中
+ mount=type=tmpfs: 将一个tmpfs文件系统挂载到指定位置
+ mount=type=secret: 挂载一些私密文件
+ mount=type=ssh: 挂载ssh的密钥文件

## 参考资料

[BuildKit](https://heychenbin.github.io/post/buildkit/)