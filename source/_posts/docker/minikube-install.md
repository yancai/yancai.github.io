---
title: minikube 安装介绍
date: 2018-08-24 15:58:18
tags:
    - kubernetes
    - minikube
---


# 简介
本文将介绍使用[minikube](https://github.com/kubernetes/minikube)单机部署[kubernetes](https://kubernetes.io/)的方法。

<!-- more -->

# 版本

 - minikube:0.28.2
 - docker:17.03.x-ce

# 安装指南

 1. 安装docker  
    详见[https://docs.docker.com/install/](详见https://docs.docker.com/install/)

 2. 安装minikube  
    执行命令
    ```bash
    curl -Lo minikube https://github.com/kubernetes/minikube/releases/download/v0.28.2/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube
    ```
 3. 下载docker镜像  
    minikube安装过程中会下载docker所需的镜像，需要下载的镜像包含：  
    ```
    gcr.io/k8s-minikube/storage-provisioner:v1.8.1
    k8s.gcr.io/etcd-amd64:3.1.12
    k8s.gcr.io/k8s-dns-dnsmasq-nanny-amd64:1.14.8
    k8s.gcr.io/k8s-dns-kube-dns-amd64:1.14.8
    k8s.gcr.io/k8s-dns-sidecar-amd64:1.14.8
    k8s.gcr.io/kube-addon-manager:v8.6
    k8s.gcr.io/kube-apiserver-amd64:v1.10.0
    k8s.gcr.io/kube-controller-manager-amd64:v1.10.0
    k8s.gcr.io/kube-proxy-amd64:v1.10.0
    k8s.gcr.io/kube-scheduler-amd64:v1.10.0
    k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.1
    k8s.gcr.io/pause-amd64:3.1
    ```

    由于网络原因以上镜像下载会经常失败，可以从我本人的阿里云镜像仓库中下载，地址为：
    ```
    registry.cn-hangzhou.aliyuncs.com/yancai/storage-provisioner:v1.8.1
    registry.cn-hangzhou.aliyuncs.com/yancai/etcd-amd64:3.1.12
    registry.cn-hangzhou.aliyuncs.com/yancai/k8s-dns-dnsmasq-nanny-amd64:1.14.8
    registry.cn-hangzhou.aliyuncs.com/yancai/k8s-dns-kube-dns-amd64:1.14.8
    registry.cn-hangzhou.aliyuncs.com/yancai/k8s-dns-sidecar-amd64:1.14.8
    registry.cn-hangzhou.aliyuncs.com/yancai/kube-addon-manager:v8.6
    registry.cn-hangzhou.aliyuncs.com/yancai/kube-apiserver-amd64:v1.10.0
    registry.cn-hangzhou.aliyuncs.com/yancai/kube-controller-manager-amd64:v1.10.0
    registry.cn-hangzhou.aliyuncs.com/yancai/kube-proxy-amd64:v1.10.0
    registry.cn-hangzhou.aliyuncs.com/yancai/kube-scheduler-amd64:v1.10.0
    registry.cn-hangzhou.aliyuncs.com/yancai/kubernetes-dashboard-amd64:v1.8.1
    registry.cn-hangzhou.aliyuncs.com/yancai/pause-amd64:3.1
    ```
    从阿里云上下载下来，然后重命名镜像即可。  
    例如：  
    如果要下载镜像`k8s.gcr.io/pause-amd64:3.1` 
    ```bash 
    # 先从对应的阿里云仓库中下载
    sudo docker pull registry.cn-hangzhou.aliyuncs.com/yancai/pause-amd64:3.1
    # 添加tag
    sudo docker tag registry.cn-hangzhou.aliyuncs.com/yancai/pause-amd64:3.1 k8s.gcr.io/pause-amd64:3.1
    # 删除原有镜像tag
    sudo docker rmi registry.cn-hangzhou.aliyuncs.com/yancai/pause-amd64:3.1
    ```

 4. 启动minikube  
    ```bash
    sudo minikube start --vm-driver=none
    ```
 5. 使用dashboard查看kubernetes  
    ```bash
    # 此命令可查看dashboard的访问地址，浏览器中打开即可
    sudo minikube dashboard --url
    ```
