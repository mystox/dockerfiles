#构建
构建xl2tpd服务容器，不启动ipsec，简单用户名密码校验
`
docker build -t cloud.mystox.tech:23760/xl2tpd-simple:1.0 .
`
#运行容器
`
docker run -d -e VPN_PASSWORD=123456 -e VPN_USER=test -p 1701:1701/udp --privileged cloud.mystox.tech:23760/xl2tpd-simple:1.0
`
- 或者使用xl2tpd-compose.yml运行，运行后可以在宿主机挂载目录``/home/xl2tp``下修改ip范围、增伤改查用户密码等相关功能
`
docker-compose -f xl2tpd-compose.yml up -d
`
#客户端相关：
- 客户端相关配置参考（centos环境）
1. 安装：

`
yum install -y xl2tpd ppp
`
2. 修改追加以下配置

```
vi /etc/xl2tpd/xl2tpd.conf

[lac test]
name = test;
lns = 47.99.145.200;
pppoptfile = /etc/ppp/peers/test.l2tpd;
ppp debug = yes;
local ip = 10.8.8.101

```

3. 新增以下配置

```

[root@centos7-base etc]# vi /etc/ppp/peers/test.l2tpd
remotename test
user "vpn"
password "vpn"
unit 0
nodeflate
nobsdcomp
noauth
persist
nopcomp
noaccomp
maxfail 5
debug

```

4. 启动服务
```
xl2tpd
```
5. 拨号
```
echo 'c test' >/var/run/xl2tpd/l2tp-control
```
6. 查看连接结果
```
ip addr

142: ppp0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1410 qdisc pfifo_fast state UNKNOWN group default qlen 3
    link/ppp 
    inet 10.8.8.101 peer 10.8.8.10/32 scope global ppp0
       valid_lft forever preferred_lft forever

```

7. 连接成功配置路由即可
```
route add -net 10.8.8.0 netmask 255.255.255.0 dev ppp0
```




