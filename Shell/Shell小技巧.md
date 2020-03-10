# Shell 小技巧
### 艺术字体 生成输出
https://my.oschina.net/u/4045573/blog/2986313
### 执行程序 5s 超时退出
`timeout 5 tcpkill -i eth0 host 172.16.6.30 and port 6379`

### kill tcp 链接
```
apt-get install dsniff
tcpkill -i eth0 host 172.16.6.30 and port 6379
```

### 清除 docker 日志
```
filepath=/var/lib/docker/containers;for logfile in `ls $filepath/*/*-json\.log`;do  > $logfile ;done
```

### shell输入输出
* 将错误输出信息关闭掉 2>&-   2>/dev/null
* 关闭所有输出 1>&- 2>&-
* & 代表标准输出 ，错误输出 将所有标准输出与错误输出 输入到/dev/null文件 &>/dev/null

### rabbitmq 关闭所有的存储连接
`./rabbitmqadmin --host 172.16.6.48 --port 15671 -u jdev -p 'dogs&cats' -f tsv -q list connections name > c.txt
while read -r name; do rabbitmqadmin --host 172.16.6.48 --port 15671 -u jdev -p 'dogs&cats' -q close connection name="${name}"; done < c.txt`
### nginx master进程limit更改
    ulimit -HSn 102400  #指定即可
    daemon $nginx -c $NGINX_CONF_FILE
如果某项服务已经启动，再动态调整ulimit是无效的，这时，可以考虑通过修改/proc/’程序pid’/limits来实现动态修改！！！
```
echo -n "Max open files=65535:65535" > /proc/5585/limits  #旧内核可以以这种方式
prlimit --pid 5585 —nofile=65535:65535   #新内核使用这种方式
```
### lvs 替换节点
添加节点在keepalive里操作reload,
去掉节点先重置权重为0，再去掉，目的批量链接断开产生的影响
```
ipvsadm -e -t 172.26.6.30:443 -r 172.26.6.43:443 -w 0
ipvsadm -e -t 172.26.6.30:80 -r 172.26.6.43:80 -w 0
```
### 查看进程启动精准时间
ps -p 14001 -o lstart

### 查看机器出口ip
curl ifconfig.me

### image ascii code 转变
将图片转换为ascii码

### 创建删除-p文件 shell delete  file with hyphen
```
mkdir -- -p  Use "--" to make rm stop parsing command line options, like this:
rmdir -- -p or rmdir ./-p
```

### Chrome 清理dns缓存
用户还可以在Chrome://net-internals/#dns清理缓存。

### shell 多线程
```
seq 1 ${all_num} | xargs -n 1 -I {} -P ${thread_num} sh -c "sleep 1;echo {}”
parallel
mkfifo
```
### docker 证书生成
```
1.给docker服务器签发证书，使用脚本默认class1.sha256，docker CA由class1签发：
     EXT=certs/docker/docker-cdszw.ext; CA=docker/docker-ca;  ./mkcert.sh certs/docker/docker-cdszw
     使用certs/docker/docker-cdszw.chain.crt和docker-cdszw.key证书
2.连接docker server需要证书，签发如下，使用docker-ca：
    CA=docker/docker-ca  ./mkcert.sh docker/docker-client

cp docker-host.chain.crt docker-host.crt
```
### salt cron管理
```
salt 'app-zw-01' cron.set_job root '0' '03' '*' '*' '*' 'rpm -Va >> /home/.rpmsave.txt' 'save "vpm -Va”'
salt 'app-zw-01' cron.raw_cron root
```
### pip代理安装软件
pip3.6 install -i https://pypi.tuna.tsinghua.edu.cn/simple 

### 容器top
docker run --rm -ti   --name=ctop  -v /var/run/docker.sock:/var/run/docker.sock  quay.io/vektorlab/ctop:latest

### 定位url 响应时间问题
`curl -o /dev/null -s -w "http_code:%{http_code} \nhttp_connect:%{http_connect} \ncontent_type:%{content_type} \ntime_namelookup:%{time_namelookup} \ntime_redirect:%{time_redirect} \ntime_pretransfer:%{time_pretransfer} \ntime_connect:%{time_connect} \ntime_starttransfer:%{time_starttransfer} \ntime_total:%{time_total} \nspeed_download:%{speed_download}" domain`


### 使用的 java8 参数
`$JAVA_OPTS" && JAVA_OPTS='-server -Xms4g -Xmx8g -XX:MetaspaceSize=512m -Duser.timezone=GMT+08 -XX:MaxMetaspaceSize=1024m -XX:NewRatio=2 -Djava.awt.headless=true`


### tmux 非交互执行命令
```
#!/bin/bash
tmux new-session -d -s mySession -n myWindow
tmux send-keys -t mySession:myWindow "cd /my/directory" Enter
tmux send-keys -t mySession:myWindow "vim" Enter
tmux attach -t mySession:myWindow
```
### zabbix数据表清除
```
truncate table history;
truncate table history_uint;
truncate table alerts;
truncate table trends_uint;
truncate table trends;
truncate table history_text;
truncate table events;

处理 events 表的时候
在Mysql中取消外键约束:  SET FOREIGN_KEY_CHECKS=0; 
再设置外键约束: SET FOREIGN_KEY_CHECKS=1;  
查询 zabbix 各表的大小 
SELECT table_name AS "Tables", round(((data_length + index_length) / 1024 / 1024), 2) "Size in MB" FROM information_schema.TABLES WHERE table_schema = 'zabbix' ORDER BY (data_length + index_length) DESC;
```

### 统计季度的repo tag
git tag --format='%(creatordate:short)%09%(refname:strip=2)'  | sort

### gilab 迁移
gitlab自带的nginx配置了cache，如果是迁移过来的git仓库，缓存也会带来，访问地址还会走缓存
需要清理cache
`gitlab-rake cache:clear RAILS_ENV=production`
然后重新启动
`gitlab-ctl restart`

### linux强制Linux扫描SCSI设备
```
echo "- - -" >  /sys/class/scsi_host/host0/scan
echo "- - -" >  /sys/class/scsi_host/host0/scan;echo "- - -" >  /sys/class/scsi_host/host1/scan;echo "- - -" >  /sys/class/scsi_host/host2/scan
```
### 网卡识别不了，重载驱动
`rmmod  ixgbe;modprobe ixgbe;/etc/init.d/network restart`

### rundeck查询应用归属地
`SELECT project,job_name,group_path FROM rundeck.scheduled_execution where job_name like "%www%";`

### docker daemon配置加速
--registry-mirror=https://u1qbyfsc.mirror.aliyuncs.com

### 计算kvm qcow2空间配置总量
`for i in `ls`;do qemu-img info $i | grep virtual;done | awk -F '[ (]' 'BEGIN{total=0}{total+=$5}END{print total/ 1024/1024/1024}'`

### kafka
* 查询 kafka 版本 `find ./libs/ -name \*kafka_\* | head -1 | grep -o '\kafka[^\n]*'`

* kafka节点重启后，可以让leader分布的更匀均一些 `./bin/kafka-preferred-replica-election.sh --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka`

* 查看__consumer_offsets 内置的topic `./bin/kafka-topics.sh --describe --topic __consumer_offsets  --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka`

* 添加log删策略

```
./bin/kafka-configs.sh --entity-type topics --entity-name __consumer_offsets --add-config cleanup.policy=delete --alter  --zookeeperzk1:2181,zk2:2181,zk3:2181/kafka
./bin/kafka-configs.sh --entity-type topics --entity-name __consumer_offsets --delete-config compression.type=producer --alter  --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka
./bin/kafka-configs.sh --describe --entity-type topics --entity-name __consumer_offsets --zookeeper zk1:2181,zk2:2181,zk3:2181/kafka
```

### docker 进入高权限 
```
 /home/user/nsenter
docker inspect --format {{.State.Pid}} docker-name
./nsenter  --target 119431 --mount --uts --ipc --net --pid
```
### 证书检测网站
https://www.trustasia.com/tools-ssl-state
https://csr.chinassl.net/ssl-checker.html

#获取一个项目的开发者贡献
`join -1 1 -2 1 <(git log --since==2017-10-01 --until=2017-10-30  --pretty='%aN'  | sort | uniq -c | sort -k1 -n -r  | sort -k 2 | awk '{print $2, " commit times:", $1}' )  <(git log --since==2017-10-01 --until=2017-10-30  --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done | sort -k 1)`

### 格式化对齐打印文件
`column -t file`

#硬盘测速
* 一般性测试
`time dd if=/dev/zero of=test.dbf bs=8k count=300000  oflag=direct; time dd if=test.dbf bs=8k count=300000 of=/dev/null ; rm test.dbf`
* 同步io测试
`time dd if=/dev/zero of=test.dbf bs=8k count=300000  oflag=dsync; time dd if=test.dbf bs=8k count=300000 of=/dev/null ; rm test.dbf`
* 同步io测试并且 sync到磁盘
`time dd if=/dev/zero of=test.dbf bs=8k count=300000 oflag=dsync  conv=fdatasync; time dd if=test.dbf bs=8k count=300000 of=/dev/null ; rm test.dbf`

### kafka状态查看
```
export JMX_PORT=10009
列出kafka topics
./kafka-topics.sh --list --zookeeper master-01.zookeeper.local:2181/kafka
end offset
./kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.16.3.35:9092 -topic logstash-gelf --time -1
start
./kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 172.16.3.35:9092 -topic logstash-gelf --time -2
```

### lvm相关命令的提示 file descriptor 3 leaked on lvm invocation
禁掉提示，设置下面的环境变量即可
`export LVM_SUPPRESS_FD_WARNINGS=1`

### nginx alias root区别
一般情况下，在location /中配置root，在location /other中配置alias是一个好习惯

### redis设置超时时间
设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接
0是关闭此设置
timeout 0

### windows扩展磁步骤
先关机 
```
qemu-img resize /data/vm-images/\win200802.system.qcow2  +10G
qemu-img info /data/vm-images/win2008-02.system.qcow2
virsh start win2008-02
```
### 删除退出的容器
`docker ps --filter status=exited -q | xargs docker rm`
### 删除无用的镜像
`docker images | grep '<none>' | awk '{print $3}' | xargs -n 1 -I {} docker rmi {}`
### docker 容器ip查看
`docker inspect -f '{{.Name}} - {{.NetworkSettings.IPAddress }}' $(docker ps -aq)`


### 查看系统文件句柄数
`cat /proc/sys/fs/file-nr `

### 命令行使用代理
```
http_proxy=IP:PORT  https_proxy=IP:PORT command
all_proxy=IP:PORT command
ALL_PROXY=IP:PORT command
当然也可是其它的协议
ALL_PROXY=socks5://127.0.0.1:1080
http_proxy="socks5://127.0.0.1:1080"
https_proxy="socks5://127.0.0.1:1080"
测试 all_proxy=IP:PORT curl ipinfo.io
```
### kvm扩展磁盘
```
方法一： 
  在kvm物理机上: `lvextend -L +100G /dev/vg_data/lvm-zw-01 `
  在虚拟机上`shutdown -h now `
  在物理机上:` virsh start lvm-zw-01 `
  在虚拟机上:   
			  pvresize /dev/vdb 
			  lvextend -L +100G /dev/vg_data/lv_data 
			  xfs_growfs /dev/vg_data/lv_data

方法二，在线扩展： 
   在kvm物理机上:       lvcreate -L 50G -n lvm-zw-01-extend vg_data 
                              virsh attach-disk lvm-zw-01 /dev/vg_data/lvm-zw-01-extend vdc --persistent
                              virsh edit lvm-zw-01 把vdc <disk></disk>段添加至xml配置文件 

   在虚拟机上: pvcreate /dev/vdc 
                       vgextend vg_data /dev/vdc 
                       lvextend -L +50G /dev/vg_data/lv_data 
                       xfs_growfs /dev/vg_data/lv_data 

qcow2 qemu-img create test-01.data.qcow2 -f qcow2 100G 
lvrename /dev/data_flash/oracle-zw-02 /dev/data_flash/oracle-zw-01
lvextend /dev/Mega/root /dev/sdb1
resize2fs /dev/Mega/root
```
### 分区，kvm透传
```
pvcreate /dev/vdb 
vgcreate vg_data /dev/vdb 
lvcreate -l 100%FREE -n lv_data vg_data 
yum -y install xfsprogs xfsdump 
mkfs.xfs /dev/vg_data/lv_data 
mkdir /data 
echo "/dev/vg_data/lv_data                      /data       xfs                 defaults        1 2" >>/etc/fstab 
mount -a 
```
### 对比两个文件夹文件
`diff -urNa dir1 dir2 `

### curl socket用法
` echo -e "GET /containers/json?all=1&before=1d74701645f3 HTTP/1.0\r\n" | nc -U /var/run/docker.sock`

### 查看 swarm 信息
`docker -H localhost:2375 --tls=true ps`
### 计算多个文件目录大小 
`du -c dir dir file file`
### 查看docker主机上容器名和ip
`docker ps -q | xargs  docker inspect --format='{{.Name}} {{.NetworkSettings.IPAddress}}’`
### java dump内存信息  
`jmap -dump:file=$APP_NAME.txt,format=b 1&&gzip $APP_NAME.txt&&ls &&ifconfig |grep Bcast|awk '{print $2}'|echo `cut -d ":" -f 2`/$APP_NAME.txt.gz&&python -m SimpleHTTPServer 80
/opt/jdk/bin/jmap -dump:format=b,file=amque_dump.txt 30500`
### 清除arp缓存 命令 
```
ip neigh flush dev eth0
arp -n|awk '/^[1-9]/{system("arp -d "$1)}'
重启机器有时候会解决一些奇怪的问题，要慎重
dns:
222.88.88.88
```

