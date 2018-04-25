---
title: docker-compose-quickstart
date: 2018-04-23 22:47:47
tags:
    - docker
    - docker-compose
---


# 简介
在了解了docker的基本用法以及使用dockerfile构建镜像后，我们可以看起来开森愉快的捣腾docker容器了。
部署个nginx，折腾个tomcat，多版本Python，整个Hadoop等等等等。
爽了一段时间，容器逐渐越来越多时候，就会发现又有了新问题。
比个如，如果要搭建个简单的web服务，使用MySQL数据库（一个容器），使用Python Flask开发web（又一个容器），使用Nginx + uwsgi做反向代理（再一个容器），数据库可能会做读写分离，web服务可能会做高可用或者负载均衡。对于这个常见的场景，最简单那就几个容器一个个启动，彼此间约定好访问的ip或者域名。使用久了就会发现比较麻烦，如果要删除重新拉起一套，一个个查容器id，停止，删除，再启动，容器多了就会超级麻烦。

一个好的办法就是用[docker-compose](https://docs.docker.com/compose/)来管理编排多个服务。

本文将简单介绍使用docker-compose编排服务的基本使用方法。

<!-- more -->

# docker-compose安装

[《Install Docker Compose》](https://docs.docker.com/compose/install/)这个里面有关docker-compose的安装方法写的挺详细的，建议时间充足阅读这个。  

这里简单介绍下快速安装docker-compose的方法。  
执行如下命令
```bash
# 下载docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.21.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose

# 添加可执行权限
sudo chmod +x /usr/local/bin/docker-compose
```

验证是否安装成功，输入如下命令，查看docker-compose版本

```bash
$ sudo docker-compose --version
```

输出版本信息
```
docker-compose version 1.21.0, build 5920eb0
```

# docker-compose使用方法

## demo示例

下面展示个简单的例子，直接上相关文件内容：

创建一个`demo`的目录，目录中包含有`docker-compose.yml`的文件，目录结构如下：
```
demo
└── docker-compose.yml
```

`docker-compose.yml`中的内容如下：
```yaml
version: '2.2'
services:
    nginx:
        image: nginx:1.13.12
        container_name: nginx
        hostname: nginx
        environment:
            - TZ=Asia/Shanghai
        ports:
            - 8000:8000
        networks:
            - front
        command: tail -f /dev/null

    python:
        image: python:2.7
        container_name: python
        hostname: python
        environment:
            - TZ=Asia/Shanghai
        networks:
            - front
            - backend
        command: tail -f /dev/null

    mysql:
        image: mysql:5.6
        container_name: mysql
        hostname: mysql
        environment:
            - TZ=Asia/Shanghai
        networks:
            - backend
        command: tail -f /dev/null

networks:
    front:
    backend:
```

先解释下如上的内容：
 1. 创建了三个容器，分别为`nginx`, `python`, `mysql`；
 2. 创建了两个网络层面，分别为`front`和`backend`；
 3. `nginx`和`python`使用网络平面`front`，`python`和`mysql`使用网络平面`backend`，`nginx`无法访问`mysql`；


## 启动和验证

进入`demo`目录，使用如下命令拉起容器：
```bash
sudo docker-compose up -d
```
看到如下内容说明拉起成功
```
Creating network "demo_front" with the default driver
Creating network "demo_backend" with the default driver
Creating python ... done
Creating mysql  ... done
Creating nginx  ... done
```

使用命令`sudo docker network ls`，可以查到有名为`demo_front`和`demo_backend`的两个网路。  
使用命令`sudo docker inspect demo_front`或`sudo docker inspect demo_backend`可以查看网络平面详细信息。  

使用命令`sudo docker exec -it python /bin/bash`，进入容器`python`。在容器`python`中测试访问`nginx`和`mysql`
```bash
ping nginx
ping mysql
```
容器`python`可以ping通容器`nginx`和容器`mysql`。 而`nginx`和`mysql`之间无法ping通。


## 停止和删除

使用如下命令可以一把停止和删除相关容器和网络，而无需手动一个个的停止再删除
```bash
sudo docker-compose down
```

## 网路问题

常见的一个问题：假如需要扩容或者增加一个python的web服务，不妨称之为`webapp02`，新的`webapp02`服务需要加入到上面的`front`网络平面中，需要和nginx以及上面的python服务相互访问。可以使用docker命令的`--link`把`webapp02`和`nginx`、`python`连接起来，更好的办法是使用docker-compose来编排新的`webapp02`和其它容器或者网络之间的关系。  

示例如下：
目录结构
```
demo-ext
└── docker-compose.yml
```

docker-compose.yml内容如下：
```yaml
version: '2.2'
services:
    webapp02:
        image: python:2.7
        container_name: webapp02
        hostname: webapp02
        networks:
            - front
        #    - backend
        environment:
            - TZ=Asia/Shanghai
        command: tail -f /dev/null

networks:
    front:
        external:
            name: demo_front
#    backend:
#        external:
#            name: demo_backend
```

进入demo-ext目录中使用命令启动
```bash
sudo docker-compose up -d
```

可以看到如下信息
```
WARNING: Some networks were defined but are not used by any service: backend
Creating webapp02 ... done
```

进入容器`webapp02`后，测试可以ping通容器`nginx`和容器`python`，而无法ping通容器`mysql`。如需访问容器`mysql`删掉上面的`#`取消注释，重新使用命令`sudo docker-compose up -d`拉起即可。


------

以上为一个简单的docker-compose使用示例，后面将会给出一个详细的`nginx`+`flask`+`mysql`的示例。