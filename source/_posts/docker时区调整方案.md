---
title: docker 时区调整方案
description: docker 时区调整方案
mathjax: true
tags:
 - docker
cover: 'https://cache.aqco.top/static/api/img/dm/1146.jpg'
---



## 1.在构建镜像时修改时区

- 1. 基于ubuntu的镜像 与 Debian 镜像不同，此类镜像中并没有包含tzdata，所以只设置环境变量并不能达到我们想要的效果，因此需要安装tzdata。

```bash
ENV TZ=Asia/Shanghai 
RUN echo "${TZ}" > /etc/timezone \ 
&& ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \ 
&& apt update \ 
&& apt install -y tzdata \ 
&& rm -rf /var/lib/apt/lists/*
``` 

- 2.基于alpine的镜像，此类镜像中已经包含tzdata，所以只需要设置环境变量即可。

```bash
ENV TZ=Asia/Shanghai
RUN apk update \
    && apk add tzdata \
    && echo "${TZ}" > /etc/timezone \
    && ln -sf /usr/share/zoneinfo/${TZ} /etc/localtime \
    && rm /var/cache/apk/*
```

## 2.在容器运行时修改时区

```bash
docker exec -it -u root 容器名或id /bin/sh
mkdir -p /usr/share/zoneinfo/Asia
exit
docker cp /usr/share/zoneinfo/Asia/Shanghai 容器ID或容器名:/usr/share/zoneinfo/Asia
docker exec -it -u root 容器名或id /bin/sh
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
