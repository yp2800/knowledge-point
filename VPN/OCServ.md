# OCServ
## OCServ 是什么
OCServ 也就是 OpenConnect，即 Cisco AnyConnect 的兼容服务端。
> 众所周知，Cisco在网络设备领域处于霸主地位，其开发的AnyConnect作为一种VPN协议，不是想封就能封的。封锁AnyConnect将对大量跨国公司的子公司与母公司的通讯造成灾难性后果。因此，虽然AnyConnect的握手特征很明显，但是依然可以正常使用。

> 而AnyConnect作为Cisco专有技术，其服务端只能运行在Cisco设备上，即如果没有购买Cisco相关设备，将无法使用AnyConnect服务端。而OpenConnect的出现解决了这一个问题，OpenConnect是一个开源项目，其目标是在相对廉价的linux设备上运行与AnyConnect协议兼容的服务端，以此来使用该协议而不需要购买Cisco专有设备。

AnyConnect目前支持 Windows 7+ / Android / IOS / Mac

## OCServ 密码文件
添加用户`ocpasswd -c /etc/ocserv/ocpasswd testuser`

## OCServ 密码文件 + OTP 
需要源码编译安装，安装依赖，编译

```
apt-get install build-essential pkg-config \
libgnutls28-dev libev-dev \
libpam0g-dev liblz4-dev libseccomp-dev libreadline-dev \
liboath-dev

./configure --with-liboath // --with-liboath otp 支持
make 
./src/ocserv -c /etc/ocserv/ocserv.conf -d 9 -f  // 启动服务

echo "HOTP/T30 testuser - $(head -c 16 /dev/urandom |xxd -c 256 -ps)" > /etc/ocserv/users.oath  //创建用户 token
```


#### 参考
* [使用Ocserv 手动搭建 Cisco AnyConnect VPN服务端](https://doubibackup.com/k9994-k3.html)
* [Openconnect服务部署](https://wwww.lvmoo.com/1048.love)
* [Set up OpenConnect VPN Server (ocserv) on Ubuntu 16.04/18.04 with Let’s Encrypt](https://www.linuxbabe.com/ubuntu/openconnect-vpn-server-ocserv-ubuntu-16-04-17-10-lets-encrypt)