# Hadoop-conf

### 0 概述

本篇说明指引读者基于虚拟机Linux环境搭建简单Hadoop分布式集群。

### 1 准备工作

#### 1.1 需要准备的硬件

Windows计算机，配备至少6GB内存与100GB固态硬盘供虚拟机使用

测试机器配置：

ThinkPad E440 | Intel i7 4712MQ CPU | 12GB |

128GB+256GB SSD, 500GB HDD | Windows 10 x64

#### 1.2 需要准备的软件

* VMWare WorkStation 15.0 Pro
* CentOS 6.5 x64 LiveDVD
> 用binDVD或其他版本安装也可



### 2 基本环境搭建

#### 2.1 系统安装
首先安装虚拟机软件与虚拟机：
* 将虚拟机文件存放到固态硬盘
* 共创建4个，分别命名为hdp-master，hdp-node-01，hdp-node-02，hdp-node-03
* 每个虚拟机配置20GB硬盘空间与2GB内存，其他默认
* 每个虚拟机上安装VMTools以开启文本共享与文件拖动传输功能

对每个系统：
    1. 打开桌面上的Install to Hard Drive，用默认配置将系统文件安装到本地
    > 其中确认选项选择Yes，且将主机名设置为对应结点名称，随后选择Write changes to disk
    2. 重启虚拟机，以root用户登录，做好用户相关配置，将Terminal放置在桌面上备用
    > 可将root密码设置为000000，普通用户用户名和密码分别设置为hadoop和0000

#### 2.2 基础环境安装

对每个系统：
1. 安装JDK 1.8并设置环境变量（若本机已有JDK环境则跳过）

下载JDK包并传至虚拟机
https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
    
```
mkdir/usr/java

cd /usr/java

tar -zxvf jdk-8u181-linux-x64.tar.gz

vi /etc/profile

    #set java environment
    JAVA_HOME=/usr/java/jdk1.7.0_79
    JRE_HOME=/usr/java/jdk1.7.0_79/jre
    CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
    PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
    export JAVA_HOME JRE_HOME CLASS_PATH PATH
    
source /etc/profile

alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_181/bin/java 300

alternatives --config java

java -version # 验证JDK是否安装成功

rpm -qa | grep ssh # 验证SSH是否安装

```

2. 安装并配置SSH（已安装，跳过安装步骤）
```

rpm -qa | grep ssh # 验证SSH是否安装

ssh localhost # 验证SSH正常工作
              # 若出现connection refused则重启SSH服务
              # service sshd restart

ssh-keygen -t rsa # 生成密钥以配置免密登录

cat id_rsa.pub >> authorised_keys # 加入授权

chmod 600 ./authorized_keys

ssh localhost # 验证免密登录

```

3. 安装Hadoop并配置

下载Hadoop安装包并传至虚拟机
http://mirror.bit.edu.cn/apache/hadoop/common/stable/hadoop-2.9.1.tar.gz

创建hadoop用户（若前面没有新建则用如下方法）

```
useradd -m hadoop -G root -s /bin/bash

passwd hadoop # 输入两次指定密码，可用0000

visudo # 修改hadoop用户的sudo权限

    ## Allow root to run any commands anywhere
    root   ALL=(ALL) ALL
 +  hadoop ALL=(ALL) ALL
```

安装Hadoop

```
tar -zxvf hadoop-2.9.1.tar.gz

mv ./hadoop-2.9.1 /usr/local/hadoop

chown -R hadoop:hadoop ./hadoop # 修改文件权限

vi /etc/profile

    #set hadoop environment
    HADOOP_HOME=/usr/local/hadoop
    HADOOP_CONFIG_DIR=/usr/local/hadoop/etc/hadoop
    PATH=$PATH:$HADOOP_HOME/bin
    export HADOOP_HOME PATH

source /etc/profile

hadoop version # 验证Hadoop安装是否成功
```

查看与配置静态IP（虚拟机中不必特别配置）

```
ifconfig # 查看本机IP 测试机器IP为192.168.179.128
```

未完待续.....

