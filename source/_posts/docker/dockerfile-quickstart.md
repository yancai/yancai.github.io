---
title: 使用dockerfile构建docker镜像快速上手指南
date: 2018-04-10 22:24:53
tags:
    - docker
    - dockerfile
---

# 简介
在了解了docker的基本使用方法后，我们可以通过容器快速准备环境并部署服务。
有些重复性的工作，例如：手动配置一些配置项，安装特定的软件包，使用命令或脚本启动特定服务等等。为了解决这些通用或者重复性的事情，我们可以通过构建一个自己个性化的镜像来固化下来这些环境信息。

本文将简单介绍使用dockerfile构建docker镜像的基本使用方法。
本文以使用[ubuntu](http://www.ubuntu.com/)的[镜像](https://hub.docker.com/_/ubuntu/)为例说明如何根据已有的镜像为基础构建自己需要的镜像。


本例中将演示以`Ubuntu16.04`为基础，安装`ssh`, `jdk`, `vim`等软件。

<!-- more -->

# 安装包准备

 - jdk-7u79-linux-x64.gz

# 目录结构

假如工作目录在：`~/dockerfiles/my-ubuntu`  
此目录中的结构如下：

```
|-- Dockerfile
`-- packages
    `-- jdk-7u79-linux-x64.gz
```

其中Dockerfile中的内容如下：
```dockerfile
FROM ubuntu:16.04

MAINTAINER yancai "yancai915@gmail.com"
ENV LANG zh_CN.UTF-8
ENV LC_ALL zh_CN.UTF-8
ENV TZ Asia/Shanghai

RUN echo "export LANG=zh_CN.UTF-8" >> /etc/profile

# 设置apt-get源
RUN echo "" > /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse" >> /etc/apt/sources.list \
    && echo "deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse" >> /etc/apt/sources.list \
    && apt-get update

# 安装常用工具 设置SSH
RUN apt-get install -y openssh-server vim telnet sudo \
    && rm /etc/ssh/ssh_host_dsa_key -rf \
    && rm /etc/ssh/ssh_host_rsa_key -rf \
    && ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key \
    && ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key \
    && useradd guest \
    && echo "guest:guest!@#" | chpasswd \
    && echo "guest   ALL=(ALL)       ALL" >> /etc/sudoers \
    && echo "root:123456" | chpasswd \
    && sed -i "s/PermitRootLogin\ without-password/PermitRootLogin\ yes/g" /etc/ssh/sshd_config

# 修改vim配置
RUN sed -i '1s/^/set encoding=utf-8\n/' /etc/vim/vimrc \
    && echo "set hls" >> /etc/vim/vimrc \
    && echo "set expandtab" >> /etc/vim/vimrc \
    && echo "set sw=4" >> /etc/vim/vimrc \
    && echo "set tabstop=4" >> /etc/vim/vimrc

RUN mkdir /root/packages \
    && mkdir /usr/java \
    && mkdir -p /usr/local/service/tomcat

# 安装jdk
ADD packages/jdk-7u79-linux-x64.gz /usr/java
RUN echo "export JAVA_HOME=/usr/java/jdk1.7.0_79" >> /etc/profile \
    && echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> /etc/profile \
    && echo "export CLASSPATH=.:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar" >> /etc/profile

RUN mkdir /var/run/sshd
EXPOSE 22 8080

ENTRYPOINT /usr/sbin/sshd -D
```

# 构建镜像

```bash
cd ~/dockerfiles/my-ubuntu

# build镜像，注意最后有一个英文半角点号
docker build --tag="my-ubuntu:1.0" .
```

稍等片刻后构建完成

# 查询镜像

使用如下命令可以看到已经构建好的镜像
```bash
docker images
```
