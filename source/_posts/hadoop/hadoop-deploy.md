---
title: Hadoop分布式部署
date: 2018-05-07 17:02:36
tags:
    - Hadoop
---

# 简介

本文将介绍分布式部署hadoop方式，各角色部署使用独立的用户  
**待完善...**

<!-- more -->

# 环境准备

 - CentOS7
 - [hadoop-2.6.5.tar.gz](https://mirrors.aliyun.com/apache/hadoop/core/hadoop-2.6.5/hadoop-2.6.5.tar.gz)
 - [zookeeper-3.4.12.tar.gz](https://mirrors.aliyun.com/apache/zookeeper/zookeeper-3.4.12/zookeeper-3.4.12.tar.gz)

# 计划

hostname | 作用
----|----
node1 | namenode, datanode, JournalNode, zookeeper
node2 | namenode, datanode, JournalNode, zookeeper
node3 | datanode, JournalNode, zookeeper, httpfs

# 安装步骤


## 配置zookeeper

```bash
useradd zookeeper


tar -xvf zookeeper-3.4.12.tar.gz

mv zookeeper-3.4.12 /opt/

chown root:root /opt/zookeeper-3.4.12/ -R

ln -vs /opt/zookeeper-3.4.12/ /opt/zookeeper

cd /opt/zookeeper

cp conf/zoo_sample.cfg conf/zoo.cfg

mkdir /var/log/zookeeper
chown zookeeper:zookeeper /var/log/zookeeper/

mkdir /var/log/zookeeper
chown zookeeper:zookeeper /var/log/zookeeper/
```

在node1上执行：echo 1 > /var/lib/zookeeper/myid
在node2上执行：echo 2 > /var/lib/zookeeper/myid
在node3上执行：echo 3 > /var/lib/zookeeper/myid

```bash
vim conf/zoo.cfg
```
底部添加如下内容
```
dataDir=/var/lib/zookeeper
server.1=node1:2888:3888
server.2=node2:2888:3888
server.3=node3:2888:3888
```

```
vim bin/zkEnv.sh
```
修改如下内容
```sh
if [ "x${ZOO_LOG_DIR}" = "x" ]
then
    ZOO_LOG_DIR="/var/log/zookeeper"
fi
```

```bash
su zookeeper -c "/opt/zookeeper/bin/zkServer.sh start"
```

```bash
su zookeeper -c "/opt/zookeeper/bin/zkServer.sh stop"
```


## SSH 配置

```bash
# 创建用户组hadoop
groupadd hadoop

# 创建用户hdfs并设置用户组为hadoop
useradd hdfs -g hadoop

# 设置hdfs密码
passwd hdfs

su hdfs

# 一路回车
ssh-keygen

# node1, node2到node1, node2, node3
# node3到node3
ssh-copy-id node1
```

## 配置HDFS
切换至root用户

 1. 安装和配置
```bash
# 解压hadoop安装包
tar -xvf hadoop-2.6.5.tar.gz

mv hadoop-2.6.5 /opt/

chown root:root /opt/hadoop-2.6.5 -R

ln -vs /opt/hadoop-2.6.5/ /opt/hadoop

mkdir /var/log/hadoop-hdfs
chown hdfs:hadoop /var/log/hadoop-hdfs

mkdir /var/lib/hadoop-hdfs
chown hdfs:hadoop /var/lib/hadoop-hdfs
```

vim hadoop-env.sh

```
export HADOOP_LOG_DIR=/var/log/hadoop-hdfs
export JAVA_HOME=/usr/java/jdk1.8.0_77
```

vim core-site.xml

```xml
<configuration>

    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/var/lib/hadoop-hdfs</value>
    </property>

    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://mycluster</value>
    </property>

    <property>
        <name>ha.zookeeper.quorum</name>
        <value>node1:2181,node2:2181,node3:2181</value>
    </property>

</configuration>
```


vim hdfs-site.xml
```xml
<configuration>

    <property>
        <name>dfs.nameservices</name>
        <value>mycluster</value>
    </property>

    <property>
        <name>dfs.ha.namenodes.mycluster</name>
        <value>node1,node2</value>
    </property>

    <property>
        <name>dfs.namenode.rpc-address.mycluster.node1</name>
        <value>node1:8020</value>
    </property>

    <property>
        <name>dfs.namenode.http-address.mycluster.node1</name>
        <value>node1:50070</value>
    </property>

    <property>
        <name>dfs.namenode.rpc-address.mycluster.node2</name>
        <value>node2:8020</value>
    </property>

    <property>
        <name>dfs.namenode.http-address.mycluster.node2</name>
        <value>node2:50070</value>
    </property>

    <property>
        <name>dfs.namenode.shared.edits.dir</name>
        <value>qjournal://node1:8485;node2:8485;node3:8485/journal</value>
    </property>

    <property>
        <name>dfs.journalnode.edits.dir</name>
        <value>/var/lib/hadoop-hdfs</value>
    </property>

    <property>
        <name>dfs.ha.automatic-failover.enabled</name>
        <value>true</value>
    </property>

    <property>
        <name>dfs.client.failover.proxy.provider.mycluster</name>
        <value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
    </property>

    <property>
        <name>dfs.ha.fencing.methods</name>
        <value>
            sshfence
            shell(/bin/true)
        </value>
    </property>

    <property>
        <name>dfs.ha.fencing.ssh.private-key-files</name>
        <value>/root/.ssh/id_rsa</value>
    </property>

    <property>
        <name>dfs.ha.fencing.ssh.connect-timeout</name>
        <value>30000</value>
    </property>

    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/var/lib/hadoop-hdfs/nn</value>
    </property>

    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/var/lib/hadoop-hdfs/dn</value>
    </property>

</configuration>
```

vim slaves
```
node1
node2
node3
```

vim mapred-site.xml
```xml
<configuration>

    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>

</configuration>
```

 2. 启动、停止journalnode
```
su hdfs -c "./sbin/hadoop-daemon.sh start journalnode"
su hdfs -c "./sbin/hadoop-daemon.sh stop journalnode"
```
使用jsp可以查到JournalNode进程


 3. 初始化namenode  
    ```
    # node1上执行
    cd /opt/hadoop
    su hdfs -c "./bin/hdfs namenode -format"
    su hdfs -c "scp -r /var/lib/hadoop-hdfs/nn hdfs@node2:/var/lib/hadoop-hdfs"
    su hdfs -c "./bin/hdfs zkfc -formatZK"
    su hdfs -c "/opt/hadoop/sbin/start-dfs.sh"
    ```

    打开(http://node1:50070)[http://node1:50070]或(http://node2:50070)[http://node2:50070]查看hdfs信息

 ## 配置HttpFs  

    创建目录
    ```bash
    mkdir /var/log/hadoop-httpfs
    chown httpfs:httpfs /var/log/hadoop-httpfs/

    mkdir /var/run/hadoop-httpfs
    chown httpfs:httpfs /var/run/hadoop-httpfs/
    ```

    vim httpfs-site.xml
    ```xml
    <configuration>

        <property>
            <name>httpfs.proxyuser.hue.hosts</name>
            <value>*</value>
        </property>

        <property>
            <name>httpfs.proxyuser.hue.groups</name>
            <value>*</value>
        </property>

    </configuration>
    ```

    vim core-site.xml 添加
    ```xml
    <property>
        <name>hadoop.proxyuser.httpfs.hosts</name>
        <value>*</value>
    </property>

    <property>
        <name>hadoop.proxyuser.httpfs.groups</name>
        <value>*</value>
    </property>
    ```

    vim httpfs-env.sh
    ```sh
    export HTTPFS_LOG=/var/log/hadoop-httpfs
    export HTTPFS_TEMP=/var/run/hadoop-httpfs
    ```

    启动或停止
    ```bash
    su httpfs -c "/opt/hadoop/sbin/httpfs.sh start"
    su httpfs -c "/opt/hadoop/sbin/httpfs.sh stop"
    ```

