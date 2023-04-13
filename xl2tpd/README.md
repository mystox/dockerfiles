构建xl2tpd服务容器，不启动ipsec，简单用户名密码校验
`
docker build -t cloud.mystox.tech:23760/xl2tpd-simple:1.0 .
`
运行容器
`
docker run -d -e VPN_PASSWORD=123456 -e VPN_USER=test -p 1701:1701/udp --privileged cloud.mystox.tech:23760/xl2tpd-simple:1.0
`
或者使用xl2tpd-compose.yml运行
