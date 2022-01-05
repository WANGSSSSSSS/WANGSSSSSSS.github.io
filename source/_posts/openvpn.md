---
title: openvpn
date: 2022-01-05 21:14:32
categories:
- net
tags:
- net
- environment
cover:
---

### openvpn

最近开始实习，远程工作需要设置一些环境，公司很多网站是设置在内网上，没有内网的访问简直寸步难行，win11,和ubuntu18应该是很稳定的，使用的系统为manjaro 21. 还是喜欢arch

我开始以为访问内网主要通过ssh服务就够了，根据我对于vpn的了解，理所当然认为我需要做的就是避免全局代理，所以一开始的办法是用docker作为vpn, 然后打开一个proxy代理，把代理端口映射下来，每次登陆的时候，使用ssh的`ProxyCommand` ， 等价于`proxyjump`  ，不过是使用`corkscrew`, 因为`nm`没成功

```sh
   Host node
        HostName xxxx.com
        User wangshuai
        ProxyCommand  corkscrew 127.0.0.1 10808 %h %p
        IdentityFile ~/.ssh/id_rsa
        IdentitiesOnly yes
        HostkeyAlgorithms +ssh-rsa
        PubkeyAcceptedAlgorithms +ssh-rsa

```

docker配置用的别人搞好的 [[GITHUB](https://github.com/wfg/docker-openvpn-client)]

```bash
docker pull ghcr.io/wfg/openvpn-client
docker run --detach \
  		   --name=openvpn-client \
 		   --cap-add=NET_ADMIN \
           --device=/dev/net/tun \
           --volume <path/to/config/dir>:/data/vpn \
       ghcr.io/wfg/openvpn-client
```

把docker的proxy端口映射到我这边，也就是10808

本来一切都准备好了，突然发现，出现了两个问题：

* 部分公司网站，访问不了（当时没意识到是dns的问题，因为对于docker不太熟）
* 有些站点是ftp,对于ftp的协议没法直接代理（需要自己写一点代码，放弃）

### openvpn 怎么非全局代理？

#### 路由表大法

使用networkmanager的openvpn插件可以获得公司的内网dns服务器地址，但是这样会导致：

* 网速很慢，因为全部选择路由到vpn server那边了

（不知道为什么，win11下面就很好，不过弱者才会投降

**解决办法**：

既然是路由选择的问题，那就搞路由，但是感觉不是很优雅。

* 修改配置文件（无效，因为这个networkmanager openvpn插件压根不支持这个feature，但是openvpn支持）

  给openvpn的配置文件添加：

```
route-nopull ;不接受服务器的路由规则
route default 0.0.0.0  net_gateway  ;定义自己的路由规则 normal
route 10.0.0.0 255.255.255.0  vpn_gateway ;定义自己的路由规则， vpn
```

​	  或者改大路由距离，这样默认访问就不会走vpn,同时一些内网规则可以走vpn：

```
route-metric 800
```

* 修改路由表

  这里是说，修改路由规则，我没用这种办法

* 修改路由距离  [[Ref](https://unix.stackexchange.com/questions/413031/changing-the-metric-of-an-interface-permanently)]

添加下面到`/etc/dhcpcd.conf` , 比vpn上的路由规则小就行

```bash
interface wlan0
metric 20
```

最后，我成功访问了，但是openvpn连接莫名其妙就会几分钟断开，即使改了`reneg-sec`，也是这样，我终于察觉到networkmanager插件比较naive了，根本不支持一些连接特性

#### openvpn自动获取dns

我要直接使用openvpn来运行了，但是似乎没法获取到内网dns，这样就会导致：

* 我可以访问部分公司网站（已经缓存+暴露在公网上的域名）
* 但我不能访问所有的公司网站（因为没获取到内网的dns）

上面是我用wireshark抓包发现的，同时也能用这个知道dns是多少，当然看`resolv.conf` 也可以，不过公司名称敏感，网站域名上有，就不贴了

当然可以把dns写死在`/etc/resolv.conf` 里面，但是这样会：

* 被其他工具的结果覆盖（dhcpcd, networkmanager）
* 需要每次开机，甚至重新联网（networkmanager会覆盖）都写
* 断开连接之后，还要删除

可以利用dhcpcd，让dhcpcd中默认有公司的dns地址，不过这样不太方便，因为要手动运行，而且不能自动删除，archlinux没有dns缓存，如果硬编码的dns在第一个，断开后，每次上网，需要等待他超时，才能使用到正常的dns服务器，加上迭代查询会让网非常慢。

```bash
resolv_conf=/etc/resolv.conf
# If you run a local name server, you should uncomment the below line and
# configure your subscribers configuration files below.
name_servers="dns.xx.xx.xx"

```

**解决办法**：

* 让openvpn给你执行特定的动作，把下面写入openvpn配置文件

```sh
up /etc/openvpn/up.sh
down /etc/openvpn/down.sh
```

* 连接的时候获得dns， 顺便保存原有的resolve.conf,  这里面是archlinux的dns地址，把它删了，网络立马说，他受到限制了，最后把新dns写入到resolve.conf上  **up.sh**: 

```bash
#! /bin/bash
DEV=$1

if [ ! -d /tmp/openvpn ]; then
mkdir /tmp/openvpn
fi
CACHE_NAMESERVER="/tmp/openvpn/$DEV.nameserver"
echo -n "" > $CACHE_NAMESERVER

dns=dns
for opt in ${!foreign_option_*}
do
eval "dns=\${$opt#dhcp-option DNS }"
if [[ $dns =~ [0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3} ]]; then
if [ ! -f /etc/resolv.conf.default ]; then
cp /etc/resolv.conf /etc/resolv.conf.default
fi

cat /etc/resolv.conf | grep -v ^# | grep -v ^nameserver > /tmp/resolv.conf
echo "nameserver $dns" >> /tmp/resolv.conf
echo $dns >> $CACHE_NAMESERVER
cat /etc/resolv.conf | grep -v ^# | grep -v "nameserver $dns" | grep nameserver >> /tmp/resolv.conf
mv /tmp/resolv.conf /etc/resolv.conf

fi
done
```

* 关闭连接的时候，恢复原有的dns,也就是resolve.conf， **down.sh**

```bash
#! /bin/bash
echo "Restoring original nameservers"
rm -f /etc/resolv.conf
cp /etc/resolv.conf.default /etc/resolv.conf
echo "Done restoring nameservers cheers"
```



