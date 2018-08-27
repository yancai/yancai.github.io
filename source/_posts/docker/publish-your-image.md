---
title: 发布你的个人docker镜像
date: 2018-08-24 16:31:43
tags:
    - docker
---

# 简介

 - 假如你有一个比较好的docker镜像怎么发布出来供大家使用呢？  
 - 你是否遇到过国外某些镜像很难下载下来呢？  
 - 你是否想过拥有一个自己的镜像仓库呢？

本文将介绍如何使用[Docker Hub](https://hub.docker.com/)和[阿里云](https://www.aliyun.com/)的容器镜像服务来创建自己的docker镜像仓库

<!-- more -->

# Dockerfile

以下为本文构建镜像时所使用的Dockerfile
```dockerfile
FROM k8s.gcr.io/etcd-amd64:3.1.12
MAINTAINER yancai yancai915@gamil.com
```
文件托管于[https://github.com/yancai/myDockerfiles/blob/master/kubernetes/etcd-amd64/Dockerfile](https://github.com/yancai/myDockerfiles/blob/master/kubernetes/etcd-amd64/Dockerfile)

# Docker Hub
 1. 打开[https://hub.docker.com](https://hub.docker.com)

 2. 注册一个Docker Hub用户，并登录
 
 3. 绑定GitHub账号  
    点击【Setting】 - 【Linked Accounts & Service】绑定Github账号  
    {% asset_img docker-hub-01.png docker-hub-01 %}

    选择Public and Private  
    {% asset_img docker-hub-02.png docker-hub-02 %}

    跳转至GitHub页面，授权即可

    授权后可以看到已经绑定成功  
    {% asset_img docker-hub-03.png docker-hub-03 %}

 4. 创建构建计划  
    点击【Create】-【Create Automated Build】，选择GitHub  
    {% asset_img docker-hub-04.png docker-hub-04 %}

    选择Dockerfile所在的仓库  
    {% asset_img docker-hub-05.png docker-hub-05 %}

    填写镜像名称和介绍信息后点击【Create】  
    {% asset_img docker-hub-06.png docker-hub-06 %}

    设置构建计划  
    选择【Type】，选择`Branch`或`Tag`  
    填写【Name】，填写`Branch`或`Tag`的名称  
    填写【Dockerfile Location】，此路径为相对于GitHub仓库代码根目录的Dockerfile所在的路径  
    填写【Docker Tag Name】，版本信息  
    完成后点击【Save Changes】保存  
    {% asset_img docker-hub-07.png docker-hub-07 %}

 5. 触发构建  
    首次保存构建信息后，如需开始构建需要手动点击【Trigger】触发构建。稍等片刻即可完成构建。  
    之后在GitHub上修改了相关代码提交后会自动触发Docker Hub构建。  
    {% asset_img docker-hub-08.png docker-hub-08 %}

 6. 下载镜像  
    构建完成后可以根据【Repo Info】和【Tags】中的镜像信息下载镜像，例如本示例下载方式为：
    ```bash
    sudo docker pull yancai/demo-image:3.1.12
    ```
    {% asset_img docker-hub-09.png docker-hub-09 %}  
    {% asset_img docker-hub-10.png docker-hub-10 %}


Docker Hub提供的镜像构建服务挺好用的，唯一就是构建好之后下载镜像时候，由于网络原因可能会十分慢。可以借用国内的一些类似Docker Hub的镜像服务来进行镜像构建和下载。  
以下将介绍如何在[阿里云容器Hub](https://dev.aliyun.com)上进行Docker镜像构建和下载。

# 阿里云 镜像容器服务

 1. 打开[https://dev.aliyun.com](https://dev.aliyun.com)
 
 2. 注册一个阿里云账号，并登录，进入[容器镜像服务页面](https://cr.console.aliyun.com/repositories)

 3. 绑定GitHub账号  
    点击【代码源】-【Github 绑定账号】进行授权绑定代码源  
    {% asset_img aliyun-hub-01.png aliyun-hub-01 %}

 4. 创建镜像仓库  
    点击【镜像仓库】-【创建镜像仓库】
    选择【地域】  
    选择【命名空间】，如果没有则点击左侧【命名空间】创建命名空间  
    填写【仓库名称】、【摘要】、【描述信息】  
    选择【仓库类型】  
    点击下一步继续  
    {% asset_img aliyun-hub-02.png aliyun-hub-02 %}

    选择GitHub，绑定Dockefile对应的仓库  
    如果有些镜像在国内拉取比较慢或者无法拉取，可以勾选【海外机构建】  
    点击【创建镜像仓库】保存镜像信息  
    {% asset_img aliyun-hub-03.png aliyun-hub-03 %}

    点击对应仓库的【管理】设置构建详情  
    {% asset_img aliyun-hub-04.png aliyun-hub-04 %}

    点击【构建】-【添加规则】  
    选择代码分支或Tag
    填写【Dockerfile目录】，此路径为相对于GitHub仓库代码根目录的Dockerfile所在的路径  
    填写【Dockerfile文件名】  
    填写【镜像版本】  
    点击【确认】保存  
    {% asset_img aliyun-hub-05.png aliyun-hub-05 %}

 5. 触发构建  
    首次保存构建信息后，如需开始构建需要手动点击【立即构建】触发构建。稍等片刻即可完成构建。  
    如果开启了【代码变更时自动构建镜像】，那么在GitHub上修改了相关代码提交后会自动触发构建。  
    {% asset_img aliyun-hub-06.png aliyun-hub-06 %}

 6. 下载镜像  
    构建完成后根据镜像的【基本信息】、【镜像版本】中的内容可以获取到下载镜像的地址。

    例如本示例镜像下载方式为：
    ```bash
    sudo docker pull registry.cn-hangzhou.aliyuncs.com/yancai/demo:3.1.12
    ```
    {% asset_img aliyun-hub-07.png aliyun-hub-07 %}

使用阿里云下载构建好的镜像，感觉就一个字：快！  
如此，国外有些镜像难以下载时候，使用阿里云间接拉取构建，下载到本地后重命名镜像名称，这样也就间接的下载了一些无法拉取下来的镜像。