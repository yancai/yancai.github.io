---
title: 使用rancher部署kubernetes介绍
date: 2018-09-10 17:02:52
tags:
    - Rancher
    - Kubernetes
---


# 简介
本文将介绍在VMware虚拟环境下使用[Rancher](https://rancher.com/)部署[Kubernetes](https://kubernetes.io/)


<!-- more -->

# 依赖版本

 - docker: `17.03.3-ce`
 - rancher: `v1.6.21`
 - kubernetes: `v1.10.5`
 - helm: `v2.8.2`

# 包含Docker的VMware虚拟环境创建

 1. 虚拟机创建  
    新建一个Linux的虚拟环境vm01，建议为CentOS7.x或Ubuntu18.04

 2. 安装docker

    安装参考: [https://docs.docker.com/install/](https://docs.docker.com/install/)

    安装好后设置镜像和dns  
    `sudo vim /etc/docker/daemon.json`
    ```json
    {
        "registry-mirrors": [
            "https://registry.docker-cn.com"
        ],
        "dns":[
            "114.114.114.114",
            "8.8.8.8",
            "8.8.4.4"
        ]
    }
    ```
 3. 创建快照
    基于vm01快照创建新的虚拟机vm02  
    在vm02中修改：  
    ubuntu `sudo vim /etc/cloud/cloud.cfg`: 设置preserve_hostname: true

    修改hostname和hosts  

hostname | 作用
---|---
vm01|部署rancher
vm02|部署kubernetes

# Rancher部署
**vm01上部署Rancher**  

 1. 安装Rancher  
    使用docker-compose拉起Rancher，docker-compose使用方法参见：[《使用docker-compose编排容器快速上手指南》](/2018/04/23/docker/docker-compose-quickstart/)  

    `docker-compose.yaml`如下
    ```yaml
    version: '2.2'
    services:
        rancher:
            image: rancher/server:v1.6.21
            container_name: rancher
            hostname: rancher
            environment:
                - TZ=Asia/Shanghai
            ports:
                - 8080:8080
    ```
 2. 配置Rancher主机  
    访问`http://vm01:8080`  

    添加主机  
    {% asset_img 01.png 01.png %}

    Rancher API地址确认  
    {% asset_img 02.png 02.png %}

    添加主机，将生成的脚本复制并在Rancher的主机**vm01**上执行  
    {% asset_img 03.png 03.png %}

    稍等片刻，rancher会自动下载相关的镜像并拉起容器  
    {% asset_img 04.png 04.png %}

# 部署Kubernetes

**vm02上部署kubernetes**

 1. 添加Kubernetes环境  
    【环境管理】-【添加环境】-选择Kubernetes  
    {% asset_img 05.png 05.png %}

 2. 获取添加kubernetes脚本  
    切换至kubernetes环境页面  
    {% asset_img 06.png 06.png %}

    添加主机，将生成的脚本复制并在Kubernetes的主机**vm02**上执行  
    {% asset_img 07.png 07.png %}

    执行后稍等片刻
    {% asset_img 08.png 08.png %}

    完成后可以查看主机上的信息
    {% asset_img 09.png 09.png %}

 3. 配置kubectl  
    ```bash
    # 安装kubectl
    curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.10.5/bin/linux/amd64/kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl
    sudo chmod +x /usr/local/bin/kubectl
    ```
    将kubectl配置写入vm02的`~/.kube/config`中
    {% asset_img 10.png 10.png %}

    {% asset_img 11.png 11.png %}

# 部署helm

**配置了kubectl配置文件的vm02上安装helm客户端**

 1. 安装helm
    ```bash
    wget https://storage.googleapis.com/kubernetes-helm/helm-v2.8.2-linux-amd64.tar.gz
    tar -xvf helm-v2.8.2-linux-amd64.tar.gz
    sudo mv linux-amd64/helm /usr/local/bin/helm
    ```

 2. 初始化helm
    ```bash
    helm init
    ```
