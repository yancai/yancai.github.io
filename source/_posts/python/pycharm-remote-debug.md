---
title: PyCharm远程调试Python代码
date: 2018-06-18 10:37:56
tags:
    - Python
    - debug
    - PyCharm
---


# 简介

本文将介绍如何使用[PyCharm](https://www.jetbrains.com/pycharm/)进行远程调试Python代码。

<!-- more -->

# 准备

 - PyCharm
 - 远程环境，本例中为linux系统

# 方法一：使用远程解释器调试
 1. 准备代码  
    以下为需要远程调试的代码`demo.py`

    ```python
    #!/usr/bin/python
    # -*- coding:utf-8 -*-

    import socket


    def hello_world(name="world!"):
        print("hello " + name)
        hostname = socket.gethostname()
        print(hostname)


    if __name__ == "__main__":
        hello_world()
        
    ```

 2. PyCharm连接到远程环境  
    点击【Tools】-【Deployment】-【Configuration】  
    {% asset_img 001.png 001.png %}

    点“+”加号，新增一个sftp配置
    {% asset_img 002.png 002.png %}

    填写host, port, User name, Password等信息，填写好后可以点击`Test SFTP connection...`来测试配置是否正确。可以点击`Autodetect`自动填写Root path，或者手动配置  
    {% asset_img 003.png 003.png %}

    点击【Mappings】来配置远程服务器目录和本地目录映射关系，填写Local path, Deployment path on server，也可以点击右侧`...`来手动选择目录。**注意，Deployment path on server填写的是以Root path为根目录的相对路径**，可以在【Excluded Paths】中填写需要被排除同步的文件或文件夹，点击OK保存配置
    {% asset_img 004.png 004.png %}

 3. 上传代码至远程服务器  
    在需要上传的代码或文件夹上点击右键-【Deployment】-【Upload to ...】，即可将代码上传至远程服务器  
    {% asset_img 005.png 005.png %}

    可以点击【Tools】-【Deployment】-【Browse Remote Host】查看远程服务器`Root path`下的目录结构
    {% asset_img 006.png 006.png %}

 4. 配置远程解释器  
    点击【settings】-【Project Interpreter】-【右上方小齿轮】-【add】
    {% asset_img 007.png 007.png %}

    点击【SSH Interpreter】-【Existing server configuration】，然后选择move，下一步 
    {% asset_img 008.png 008.png %}

    填写Interpreter（远程服务器Python解释器路径）,以及Sync folders（本地目录和远程目录的映射关系），点击Finish完成
    {% asset_img 009.png 009.png %}

 5. 开始调试或运行  
    在保证远程Python解释器环境安装了工程所需的依赖包后，开始调试或者运行即可


# 方法二：使用pycharm-debug包调试

 1. 准备代码  
    以下为需要远程调试的代码`demo.py`，将代码上传至远程服务器上
    ```python
    #!/usr/bin/python
    # -*- coding:utf-8 -*-

    import socket


    def hello_world(name="world!"):
        print("hello " + name)
        hostname = socket.gethostname()
        print(socket.gethostname())


    if __name__ == "__main__":
        hello_world()

    ```

 2. 远程服务器上安装`pytharm-debug.egg`（Python2.x适用）或`pycharm-debug-py3k.egg`（Python3.x适用）  
    安装包位于pycharm安装目中的`debug-eggs`目录中，将需要的安装包上传至远程服务器上，使用如下命令安装依赖包：
    ```shell
    easy_install pycharm-debug.egg
    ```

 3. 在本地pycharm中新建远程调试配置  
    {% asset_img 010.png 010.png %}

    新建`Python Remote Debug`
    {% asset_img 011.png 011.png %}

    填写本地ip，端口（本地启动调试服务的端口，不和已有端口冲突即可），远程和本地目录映射关系（此处可以不配置，调试启动是选择Download远程代码也可以）  
    {% asset_img 012.png 012.png %}

4. 修改远程需要调试的代码

    将如下代码添加至远程需要被调试的代码中，添加至需要开始调试的地方
    ```python
    import pydevd
    pydevd.settrace('10.60.84.78', port=6666, stdoutToServer=True, stderrToServer=True)
    ```

    例如：
    ```python
    #!/usr/bin/python
    # -*- coding:utf-8 -*-

    import socket

    import pydevd


    def hello_world(name="world!"):
        print("hello " + name)
        pydevd.settrace('10.60.84.78', port=6666, stdoutToServer=True, stderrToServer=True)
        hostname = socket.gethostname()
        print(hostname)


    if __name__ == "__main__":
        hello_world()

    ```

 5. 远程代码启动  
    ```shell
    python demo.py
    ```
    启动后，运行至`pydevd.settrace(...`处会等待pycharm启动debug

 6. PyCharm启动Remote Debug  
    {% asset_img 013.png 013.png %}

    此时若没有配置目录映射，则会提示如下信息：
    {% asset_img 014.png 014.png %}
    选择Download可以下载远程代码，也可以配置目录映射  

    之后可进入到开始调试了的入口，可以开始调试啦
    {% asset_img 015.png 015.png %}

