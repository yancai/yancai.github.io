---
title: WSL安装及WSL Linux环境常用配置手册
date: 2026-07-22 20:08:00
description: 介绍Windows上安装WSL及配置WSL虚拟机的基本方法
tags:
    - WSL
    - Linux
    - Ubuntu
    - config
---

# WSL安装方式

1. 安装方式

   ```powershell
   # 根据实际情况修改参数
   # -d指定发行版本名称
   # --name设置虚拟机实例名称，实例名称不可重复
   # --location设置虚拟机安装位置
   wsl --install -d Ubuntu-26.04 --name Ubuntu-26.04 --location D:\wsl\Ubuntu
   ```

2. 打开`WSL Settings`软件，**网络**-**网络模式**设置为`Mirrored`，这样的话WSL中虚机和主机共享一个网络

3. WSL备份与恢复

   ```powershell
   # 备份虚拟机，根据实际情况修改导出位置
   wsl --export Ubuntu-26.04 D:\wsl\snapshot\ubuntu-26.04-snapshot-202606082220.tar
   
   # 从备份恢复，第一个位置为虚机文件位置，第二个为备份文件位置
   wsl --import Ubuntu-26.04-test D:\wsl\ubuntu-test D:\wsl\snapshot\ubuntu-26.04-snapshot-20260608.tar
   ```

# Linux常见工具安装及配置方式

其他Linux系统初始化也可以参考。

## 建议安装配置

### 安装常用的软件

```bash
# 更新apt源
sudo apt update

# 安装以下常用软件
sudo apt install plocate zip net-tools telnet python3.14-venv pipx
```

### 设置默认语言环境

否则带有中文的文件名输出会乱码。

```bash
sudo locale-gen en_US.UTF-8
sudo update-locale LANG=en_US.UTF-8
```

### 安装bun

bun是目前最流行最快的JavaScript包管理工具。

```bash
curl -fsSL https://bun.sh/install | bash
```

使用`source ~/.bashrc`或退出重新进入虚机，输入`bun -v`可看到输出版本号即说明安装成功。

### 安装uv

uv是目前最流行最快的Python包管理工具。

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

使用`source ~/.bashrc`或退出重新进入虚机，输入`uv --version`可看到输出版本号即说明安装成功。

### 安装nvm

nvm是node.js多版本管理工具。

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.6/install.sh | bash
```

注意根据需要修改以上脚本中的版本号，最新版本可参见[`https://github.com/nvm-sh/nvm`](https://github.com/nvm-sh/nvm)

使用`source ~/.bashrc`或退出重新进入虚机，输入`nvm --version`可看到输出版本号即说明安装成功。

### 安装OpenCode

OpenCode是类似ClaudeCode、CodeX的开源平替工具

```bash
bun add -g opencode-ai
```

输入`opencode -v`可看到输出版本号即说明安装成功。

#### 配置OpenCode大模型

使用`opencode`命令进入OpenCode，输入`/connect`选择大模型供应商，配置API Key即可使用。

OpenCode同样提供OpenCode Go和OpenCode Zen大模型服务，这两个服务聚合力主流的大模型，可按需切换使用。OpenCode Go相对便宜，聚合了大量开源优质大模型；OpenCode Zen相对贵一些，聚合了大量闭源优质模型。可访问[https://opencode.ai/](https://opencode.ai/)了解更多。

#### 安装oh-my-opencode插件

oh-my-opencode是OpenCode的增强插件。

打开OpenCode，粘贴如下内容并执行即可

```
Install and configure oh-my-opencode by following the instructions here:
https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/refs/heads/master/docs/guide/installation.md
```



### 安装MiniCode

MiMoCode是小米参考OpenCode开发的CLI工具，对中文支持友好。

```bash
curl -fsSL https://mimo.xiaomi.com/install | bash
```

使用`source ~/.bashrc`或退出重新进入虚机，输入`mimo -v`可看到输出版本号即说明安装成功。

## 可选配置

### 设置网络代理脚本

将以下脚本追加至`~/.bashrc`中，这样可以使用命令`proxy_on`开启代理，`proxy_off`关闭代理，`proxy_status`查看代理状态，也可以使用`curl https://www.google.com`测试是否代理生效。

```bash
# 开启代理 (默认地址为 http://127.0.0.1:7897，请根据你的实际情况修改)
proxy_on() {
    # 如果传入了参数，则使用传入的参数作为代理地址；否则使用默认地址
    local proxy_url=${1:-"http://127.0.0.1:7897"}

    export http_proxy="$proxy_url"
    export https_proxy="$proxy_url"
    export HTTP_PROXY="$proxy_url"
    export HTTPS_PROXY="$proxy_url"

    # 设置不走代理的本地地址 (非常重要，否则本地服务可能无法访问)
    export no_proxy="localhost,127.0.0.1,::1"
    export NO_PROXY="localhost,127.0.0.1,::1"

    echo "✅ 代理已开启: $proxy_url"
}

# 关闭代理
proxy_off() {
    unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY no_proxy NO_PROXY
    echo "❌ 代理已关闭"
}

# 查看当前代理状态
proxy_status() {
    if [ -n "$http_proxy" ]; then
        echo "🌐 当前代理状态: 已开启"
        echo "   代理地址: $http_proxy"
    else
        echo "🚫 当前代理状态: 未开启"
    fi
}
```



### 取消磁盘挂载

**[WSL建议配置]**

WSL默认会挂载主机上的磁盘至`/mnt`目录中，将以下脚本追加至`~/.bashrc`中可以取消默认挂载指定盘。如果不取消挂载，那么从WSL中会直接访问到主机文件，有误操作风险。WSL中安装了一些搜索软件（如`plocate`），因为挂载了主机上的磁盘文件，索引速度会变慢。如果没有必要，建议取消所有挂载。

```bash
# 卸载主机磁盘
if mountpoint -q /mnt/c; then
    sudo umount /mnt/c;
fi

if mountpoint -q /mnt/d; then
    sudo umount /mnt/d;
fi

if mountpoint -q /mnt/e; then
    sudo umount /mnt/e;
fi

if mountpoint -q /mnt/f; then
    sudo umount /mnt/f;
fi
```

设置取消挂载免密，否则每次进入系统会要求输入密码，创建文件`sudo vim /etc/sudoers.d/umount-wsl`，文件内容如下：

```
# 增加如下内容，注意xxxx需要改为对应的用户名
xxxx ALL=(ALL) NOPASSWD: /usr/bin/umount
```

### 设置pip、uv镜像源

**[建议配置]**

设置pip包镜像源为阿里云镜像源，可加速Python包下载速度，设置后同样适用于uv

```
mkdir ~/.pip

vim ~/.pip/pip.conf
```

在`~/.pip/pip.conf`中添加如下内容：

```
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

### 设置npm、bun镜像源

**[建议配置]**

设置npm包镜像源为阿里云镜像源，可加速node.js包下载速度，设置后同样适用于bun

```bash
npm config set registry https://registry.npmmirror.com
```



