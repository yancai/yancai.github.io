---
title: Node.js安装配置
date: 2018-04-22 22:59:43
tags:
    - Node.js
    - npm
    - yarn
---

# 简介

本文简介Node.js的安装和npm, yarn的安装与代理配置方法。


<!-- more -->

# 安装包准备

下载Node.js安装包: [node-v8.11.1-linux-x64.tar.xz](https://nodejs.org/dist/v8.11.1/node-v8.11.1-linux-x64.tar.xz)

# 安装配置Node.js

解压并创建软连接
```bash
# 解压至opt目录
tar -xvf node-v8.11.1-linux-x64.tar.xz -C /opt

# 建立软连接
ln -vs /opt/node-v8.11.1-linux-x64 /opt/node
```

将nodejs目录添加至环境变量  
在`~/.zshrc`或`~/.bashrc`中添加如下内容
```bash
export NODE_HOME=/opt/node
export PATH=$PATH:$NODE_HOME/bin
```

使用`source ~/.zshrc`或`source ~/.bashrc`导入环境变量
输入`node -v`查看版本可以看到版本信息
```
> node -v
v8.11.1
```


# 配置cnpm

使用淘宝的npm（[TAONPM](https://npm.taobao.org/)）镜像下载包时候很快

在`~/.zshrc`或`~/.bashrc`中添加如下内容
```
alias cnpm="npm --registry=https://registry.npm.taobao.org --cache=$HOME/.npm/.cache/cnpm --disturl=https://npm.taobao.org/dist --userconfig=$HOME/.cnpmrc"
```

使用`source ~/.zshrc`或`source ~/.bashrc`导入环境变量，即可使用`cnpm`命令，用法和`npm`一致，安装包时候下载会从淘宝镜像下载，速度杠杠的！

# 安装与配置yarn

这里说的yarn是facebook推出的Node.js包管理工具[yarn](https://yarnpkg.com/)，和Hadoop家族中的[Hadoop YARN](https://hadoop.apache.org/)是不一样的东西。  
yarn的包安装速度比npm快不少。  


使用cnpm安装yarn
```bash
cnpm install yarn -g
```


将yarn目录添加至环境变量  
在`~/.zshrc`或`~/.bashrc`中添加如下内容
```bash
export PATH=$PATH:$HOME/.yarn/bin
```

使用`source ~/.zshrc`或`source ~/.bashrc`导入环境变量，即可使用`yarn`命令。  

yarn可以使用上面提到的淘宝的npm镜像，这样yarn就更快了，配置yarn镜像方法很简单，使用命令
```bash
yarn config set registry https://registry.npm.taobao.org
```
或者在`~/.yarnrc`中添加：
```
registry "https://registry.npm.taobao.org"
```

之后即可使用yarn来安装管理包啦

# 快速链接

 - [Node.js](https://nodejs.org)
 - [yarn](https://yarnpkg.com)
