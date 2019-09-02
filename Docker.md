# Docker
关于 Docker 从 1.3之后很少关注新的特性了，无意中发现 `multi-stage builds` 多阶段构建。

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

