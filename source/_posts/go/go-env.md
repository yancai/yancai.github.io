---
title: go开发环境配置
date: 2018-09-05 15:30:57
tags:
    - go
---


# 简介
本文介绍[Go](https://golang.org/)开发环境的配置方法

<!-- more -->

# SDK 安装

 1. 安装包下载  
    下载地址：[https://golang.org/dl/](https://golang.org/dl/)
    下载`Windows`、`Linux`或`Mac`平台下的安装包

 2. 安装SDK  
    `Linux`, `Mac`上执行
    ```bash
    # 注意修改包名
    tar -C /usr/local -xzf go$VERSION.$OS-$ARCH.tar.gz
    ```

    `Windows`上执行exe进行安装

 3. 配置环境变量  
    `Linux`, `Mac`
    ```bash
    export GOHOME=/usr/local/go
    export PATH=$PATH:$GOHOME/bin
    export GOPATH=$HOME/go
    export PATH=$PATH:$GOPATH/bin
    ```

    `Windows`  
    新建环境变量`GOHOME`，值为Go安装位置  
    新建环境变量`GOPATH`，设置并创建一个路径，例如：`D:\AppData\go`  
    `PATH`中追加：`;%GOHOME%\bin;%GOPATH%\bin;`  

 4. 验证  
    命令行中输入`go version`可以正常查看go版本信息
    
# GoLand

 1. 下载GoLand安装包  
    下载地址：[https://www.jetbrains.com/go/download/](https://www.jetbrains.com/go/download/)

 2. 安装GoLand  
    执行安装包，根据提示安装

 3. 下载go工程  
    可以下载我的示例工程：
    ```bash
    go get github.com/yancai/hello-go
    ```
    下载好后，所在位置为`$GOPATH/src/github.com/yancai/hello-go`

 4. 命令行构建运行  
    ```bash
    # 命令行中执行
    go build github.com/yancai/hello-go
    # 在命令行当前目录中会生成 可执行文件hello-go 或 hello-go.exe
    # Linux, Mac执行如下
    ./hello-go
    # Windows执行如下
    hello-go.exe

    # 输出：
    # Hello go!
    ```

 5. 使用GoLand开发  
    【Open Project】 -> 选择工程目录`$GOPATH/src/github.com/yancai/hello-go`，打开工程目录即可完成工程导入  
    开发Debug方式和JetBrains其他产品（IDEA、PyCharm）类似  

# 其它

 1. 开发过程中，如需下载第三方包，执行如下命令
    ```bash
    go get xxx
    ```
    