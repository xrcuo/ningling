---
title: PVE 主机及 LXC 容器开启 DHCPv6 客户端教程
categories: pve
description: 容器开启 DHCPv6 客户端教程
mathjax: true
tags:
  - Linux
  - IPv6
  - 虚拟机
  - 软路由
  - PVE
  - DHCPv6
  - SLAAC
  - LXC
swiper_index: 3
cover: 'https://cache.aqco.top/static/blog/img/48.webp'
---
很多人配置好 PVE 之后，发现自己的 PVE 主机没有 IPv6 地址，无法远程访问，排查了很久依然无法获取地址，这其实是因为 PVE 内置的安全策略导致的，必须开启 IPv6 相关报文转发才能正常通过 DHCPv6 或 SLAAC 获取 IPv6 地址


按下面的操作修改配置文件后即可正常获取 IPv6。
注意：本教程是让你的 PVE 主机 和 LXC 容器获取 IPv6 地址，如果你虚拟机和其他下连设备出现问题，请先确定是不是你自己的问题

### 修改配置文件
```
nano /etc/sysctl.conf
```
### 把文件内容替换为/在末尾加上下面的内容

```
net.ipv6.conf.all.accept_ra=2
net.ipv6.conf.default.accept_ra=2
net.ipv6.conf.vmbr0.accept_ra=2
net.ipv6.conf.all.autoconf=2
net.ipv6.conf.default.autoconf=2
net.ipv6.conf.vmbr0.autoconf=2
```

### 重启主机

另外备注一下：LXC 容器 获取 IPv6 地址要设置成 SLAAC 才能获取地址(设置为 SLAAC 后既可以 DHCPv6 有状态，也可以 SLAAC 无状态，设置为 DHCP 只能拿到Link-Local 链路地址)
LXC 容器修改完网络设置重启对应容器即可

重启之后检查一下 PVE 主机和 LXC 容器有没有拿到地址

```
# ip a
4: vmbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether e8:9c:06:a1:19:ce brd ff:ff:ff:ff:ff:ff
    inet 192.168.2.3/24 scope global vmbr0
       valid_lft forever preferred_lft forever
    inet6 2409:8s33:391:f151:ea8e:6ff:fea5:19ce/64 scope global dynamic mngtmpaddr 
       valid_lft 219052sec preferred_lft 132652sec
    inet6 fe80::ea8e:6ff:fea5:19ce/64 scope link 
       valid_lft forever preferred_lft forever
```

可以看到同时通过 DHCPv6 和 SLAAC 拿到了地址 
IPV6前面有24开头，那就是拿到IPV6了
