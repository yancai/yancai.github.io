---
title: docker快速上手指南
date: 2018-04-09 22:43:13
description: 简介docker的基本使用方法
tags:
    - docker
---

本文将简单介绍docker的基本使用方法。本文以使用[nginx](https://nginx.org/)的[镜像](https://hub.docker.com/_/nginx/)为例说明如何快速上手使用docker。

<!-- more -->

# docker 安装
参见: [https://docs.docker.com/install/](https://docs.docker.com/install/)

# 拉取镜像

```bash
docker pull nginx
```

# 启动容器

```bash
# 启动一个nginx容器，主机的8000转发至容器的80端口
docker run -d --name my-nginx -p 8000:80 nginx
```

# 测试访问nginx

浏览器访问`http://{主机ip}:8000`，可以看到**Welcome to nginx!**页面

# 停止容器

```bash
# 查询已经启动的容器
docker ps
```
例如查到类似如下信息：

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
b8eca8f07f68        nginx               "nginx -g 'daemon ..."   24 seconds ago      Up 23 seconds       0.0.0.0:8000->80/tcp   my-nginx
```

```bash
# 使用my-nginx对应的ContainerID停止容器
docker stop {container_id}
```

# 删除容器

```bash
# 使用my-nginx对应的ContainerID删除容器
docker rm {container_id}
```
# 注意

 1. 如果容器已经停止，可以使用命令`docker ps -a`查询所有包含已停止的容器;
    
