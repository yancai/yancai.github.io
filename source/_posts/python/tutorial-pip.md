---
title: tutorial-pip
date: 2019-02-15 21:41:59
tags:
    - Python
    - pip
---

# 简介

本文将介绍：

- Python包是什么？
- 如何安装Python包？
- 以及Python的常用包管理工具[pip](https://pip.pypa.io/en/stable/)(Package Installer for Python)的常用方法和配置方式。

<!-- more -->

# Python包

在常见的语言中，都会将一个或一组功能封装好，以包的形式发布复用。比如：Java使用maven构建jar包、war包；Node.js使用npm安装管理包。

Python中也有包这一概念。Python包的格式也是经历了几代的进步，最早有`.egg`包（Python是大蟒，egg是蛋，那么Python的包就叫做egg），后来有`.whl`包，直到如今很多包使用`.zip`、`.tar.gz`。如今的Python包已经越来越好用了。

# Python包安装方法

Python包如何安装呢？以下将介绍一下Python包的手动安装方式。

> 安装Python包，绝大多数情况下建议使用本文后半部分的pip方式安装，只在万不得已的情况下使用手动安装。例如：只有安装包，而没有网络时候。

以下将介绍如何手动安装Python中一个大名鼎鼎的包——[`requests`](http://python-requests.org/)，其它包的安装方式类似，request包下载地址如下：  
[https://files.pythonhosted.org/packages/52/2c/514e4ac25da2b08ca5a464c50463682126385c4272c18193876e91f4bc38/requests-2.21.0.tar.gz](https://files.pythonhosted.org/packages/52/2c/514e4ac25da2b08ca5a464c50463682126385c4272c18193876e91f4bc38/requests-2.21.0.tar.gz)

下载好包之后，在任意目录解压开，可以看到如下的目录结构：

```
`-- requests-2.21.0
    |-- HISTORY.md
    |-- LICENSE
    |-- MANIFEST.in
    |-- Pipfile
    |-- Pipfile.lock
    |-- PKG-INFO
    |-- pytest.ini
    |-- README.md
    |-- requests
    |-- requests.egg-info
    |-- setup.cfg
    |-- setup.py
    `-- tests
```

其中`setup.py`是一个很关键的文件

在`setup.py`**同级目录**中使用如下命令即可安装`requests`包

```bash
python setup.py install
```

注意：

1. 务必在`setup.py`**同级目录**中执行如上命令，否则会有可能导致安装失败
2. 安装时打印的日志如果没有异常，那么安装就顺利完成啦
3. 不同平台由于用户权限的问题，在执行`python setup.py install`时，可能需要`root`权限，在最前面加`sudo`，即`sudo python setup.py install`
4. 由于Python包之间可能存在依赖关系，安装A包时候如果依赖了B包，但是B包如果没有安装，那么A包会安装失败，所以还是建议使用更高级的pip来进行包管理

# pip是神马？PyPI是神马？

> pip is the package installer for Python. You can use pip to install packages from the Python Package Index and other indexes.

以上这段是[pip官方](https://pip.pypa.io/)的一段话，解释了pip和PyPI是什么，以及两者之间的关系。

简单的来讲，pip（Package Installer for Python）就是Python的安装工具。使用pip可以从[PyPI（Python Package Index）官方索引](https://pypi.org/)下载并安装Python包，或者从其他镜像源下载。

使用pip安装Python包的时候，可以解决包依赖之间的问题，也可以一次下载安装多个Python包，目前pip已经是Python官方所支持的包安装工具。在某些环境（例如Windows）上安装Python时，已经自带pip工具。

常用的PyPI镜像如下：

- PyPI官方：[https://pypi.org/](https://pypi.org/)
- 阿里PyPI镜像源：[https://mirrors.aliyun.com/pypi/simple/](https://mirrors.aliyun.com/pypi/simple/)
- 网易PyPI镜像源：[http://mirrors.163.com/pypi/simple/](http://mirrors.163.com/pypi/simple/)
- 豆瓣PyPI镜像源：[https://pypi.doubanio.com/simple/](https://pypi.doubanio.com/simple/)

# pip的安装方法

## Windows系统安装pip

Windows上安装包目前已经自带了pip，安装时勾选pip即可。  
{% asset_img 01.jpg 01.jpg %}  

## Ubuntu系统安装pip

Ubuntu上通常并没有自带pip，使用apt即可安装pip，命令如下：

```bash
# 安装Python3对应的pip
sudo apt install python3-pip

# 安装Python2对应的pip
sudo apt install python-pip
```

## CentOS系统安装pip

通常yum的官方仓库中木有pip，呵呵  
so~  
参考下面的通用安装pip方法

## 通用安装pip方法

由于各平台包管理工具对于pip支持不尽相同，因此有时候不得不使用手动方式安装pip，手动安装方式在`Windows`、`CentOS`、`Ubuntu`、`Mac`等系统上均适用。

1. 下载安装包  
    从PyPI搜索并下载[`https://pypi.org/`](https://pypi.org/)，下载时候如果没有特殊要求，那就直接下载最新的稳定版本；

    **或者**从点击如下超链接直接下载：
    - [`pip-19.0.3.tar.gz`](https://files.pythonhosted.org/packages/36/fa/51ca4d57392e2f69397cd6e5af23da2a8d37884a605f9e3f2d3bfdc48397/pip-19.0.3.tar.gz)

2. 安装pip  

    ```bash
    # 解压pip包
    tar -xvf pip-19.0.3.tar.gz

    # 进入目录
    cd pip-19.0.3

    # 安装pip
    python setup.py install
    ```

    **注意**：  
    不同平台上由于Python环境不同，可能有些环境上安装pip时会报缺少`setuptools`，例如：

    ```
    Traceback (most recent call last):
    File "setup.py", line 6, in <module>
        from setuptools import find_packages, setup
    ImportError: No module named setuptools
    ```

    在这种情况下，需要先手动安装`setuptools`，可以从[PyPI](https://pypi.org/)搜索下载，或点击此处[`setuptools-40.8.0.zip`](https://files.pythonhosted.org/packages/c2/f7/c7b501b783e5a74cf1768bc174ee4fb0a8a6ee5af6afa92274ff964703e0/setuptools-40.8.0.zip)下载安装包。

# pip的常用操作

1. 搜索包  

    ```bash
    # 搜索 名称为xxx 的包
    pip search xxx
    ```

    示例：搜索名为request的包

    ```
    $ pip search requests
    requests-hawk (1.0.0)                  - requests-hawk
    requests-dump (0.1.3)                  - `requests-dump` provides hook functions for requests.
    ...
    requests (2.21.0)                      - Python HTTP for Humans.
    ...
    ```

2. 安装包  

    ```bash
    # 安装 名称为xxx 的包
    pip install xxx
    ```

    示例

    ```bash
    # 安装 名称为requests 的包
    pip install requests

    # 安装 名称为requests，版本为2.21.0 的包
    pip install requests==2.21.0

    # 安装 名称为requests，版本号小于等于2.20 的包
    pip install 'requests<=2.20'

    # 安装 名称为requests，版本号大于等于2.10且小于2.15 的包
    pip install 'requests>=2.10,<2.15'
    ```

3. 卸载包  

    ```bash
    # 卸载 名称为xxx 的包
    pip uninstall xxx
    ```

    示例

    ```bash
    # 卸载 名称为requests 的包
    pip uninstall requests
    ```

4. 列出当前环境已安装的Python包  

    ```
    $ pip list
    Package               Version
    --------------------- ----------
    pip                   18.0
    pipenv                2018.11.26
    requests              2.21.0
    setuptools            39.0.1
    virtualenv            16.0.0
    ...
    ```

# pip镜像配置

使用pip安装包时，默认会从[PyPI](https://pypi.org/)官方下载包。由于网络的原因，通常会下载比较慢或者无法下载。为了解决这个问题，可以修改默认的pip下载镜像源，改为速度较快的镜像源。

以下示例为修改pip镜像源为[阿里巴巴开源镜像站](https://opsx.alibaba.com/mirror)的PyPI镜像源（若使用其它镜像源，配置方法类似）：

- 在`Linux`系统或`Mac`系统创建文件：`~/.pip/pip.conf`
- 在`Windows`系统创建文件：`%USERPROFILE%\pip\pip.ini`

具体文件内容如下：

```ini
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/

[install]
trusted-host=mirrors.aliyun.com
```

按照如上配置后，再使用pip安装包时，就会从指定的镜像源下载包了，然后就可以开始愉快的玩耍啦~
