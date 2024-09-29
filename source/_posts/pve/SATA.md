---
title: PVE 直通硬盘（全盘映射方式）
categories: pve
description: 全盘映射方式
mathjax: true
tags:
  - Linux
  - 虚拟机
  - 软路由
  - PVE
swiper_index: 2
cover: 'https://cache.aqco.top/static/blog/img/default_cover_143.webp'
---
使用PVE有时为了方便，需要将硬盘直通，一般有两种方式，一是硬件直通，一是全盘映射，这里介绍第二种 由于硬件直通需要把直通分组才能把按照PVE的系统盘隔离出来，方法如下
### 打开PVE管理网页Shell
```
ls /dev/disk/by-id
```
查看存储设备的id
![image](https://cache.aqco.top/static/blog/img/screenshot20231215.png)
图上划红线即为硬盘ID号，复制下来
{% tip info %}info{% endtip %}
### 硬盘映射
注意：这里需要将100换成虚拟机的真实ID，sata1这里也可以换成未占用的id数（PVE支持satat0-5）
```
qm set 100 -sata1 /dev/disk/by-id/ata-WDC_XXXX_XXXX_XXXX
```
如果返回以下信息,说明已成功映射

{% tip success %}update VM 100: -sata1 /dev/disk/by-id/ata-WDC_XXXX_XXXX_XXXX{% endtip %}
### 确定是否成功
进入PVE对应虚拟机的硬件页面，查看是否硬盘是否已经在虚拟机里，如图所示说明已成功，这时打开虚拟机就能找到对应硬盘
![image](https://cache.aqco.top/static/blog/img/screenshot20231216.png)


