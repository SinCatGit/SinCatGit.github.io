---
layout: post
title: centos常用配置
author: sincat
date: 2018-07-17 14:41:15 +0800
category: 技术
tags: centos 

published: true
---

## 添加用户 ##

登录root用户

    adduser jon //增加用户 jon
    passwd jon  //设置 jon 用户密码

vi /etc/sudoers

在文件中新增一行

    root ALL=(ALL) ALL
    jon ALL=(ALL) ALL  //这一行是新增行


使用su - jon，切换到 jon 用户环境下

## 更改yum源 ##

1.备份
    
    sudo yum install wget //安装wget
    sudo yum install vim  //安装vim
    mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

2.下载最新CentOS-Base.repo 到/etc/yum.repos.d/
    
    wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

3.清理更新缓存

    sudo yum clean all //清除缓存
    sudo yum makecache  //生成缓存

## 安装设置ssh ##

1.安装openssh-server

    sudo yum install -y openssh-server
    
2.修改配置文件
用vim打开配置文件/etc/ssh/sshd_config

    Port 22 # 监听端口
    Protocol 2  # 启用SSH版本2协议
    ListenAddress 0.0.0.0 # 监听地址
    AllowUsers jon  # 允许访问用户(用空格隔开)
    PermitRootLogin no # 不允许root远程ssh
    PermitEmptyPasswords no # 用户登录需要密码认证
    PasswordAuthentication yes # 启用口令认证方式

3.重启SSH

    sudo service sshd restart

4.设置开机自启

    sudo chkcofig sshd on

## 安装zsh ##

1.安装zsh

    yum -y install zsh

2.切换默认zsh

    chsh -s /bin/zsh
    
3.安装oh my zsh

    sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
    
    vim ~/.zshrc # 可以修改主题

默认主题
    
    ZSH_THEME="robbyrussell"

## 安装git ##

    sudo yum install -y git

## 安装tmux ## 

1.安装依赖

    yum -y install ncurses-devel libevent-devel gcc
    
2.安装tmux

    git clone https://github.com/tmux/tmux.git //获取源码
    cd tmux
    sh autogen.sh
    
执行过程中可能会报中不到 aclocal 命令的错误，此时可安装 automake 后再执行 autogen.sh

    sudo yum install -y automake
    
配置安装tmux

    ./configure && sudo make install

## 安装python及pip ##

centos自带python 2.7.5
    
    sudo yum install python-devel # 安装python开发相关包

安装pip

    curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
    sudo python get-pip.py

设置pip国内源

    sudo vim /etc/pip.conf

添加如下内容，设置pip源为豆瓣源

    [global]
    trusted-host = pypi.douban.com
    index-url = https://pypi.douban.com/simple
    

## 安装supervisor ##

supervisor是python开发的一个client/server服务，是linux下的一个进程管理工具。

    sudo pip install supervisor
    su - root
    echo_supervisord_conf > /etc/supervisord.conf
    su - jon
    sudo supervisord -c /etc/supervisord.conf # 配置supervisor
    sudo supervisorctl # 查看supervisor管理的程序
    

## 安装nginx ##

安装nginx

    sudo yum install epel-release
    sudo yum install nginx
    
启动nginx

    sudo /usr/sbin/nginx # 启动nginx
    sudo systemctl start nginx # 启动nginx
    sudo systemctl enable nginx.service # 开机自启动nginx
    curl 127.0.0.1 # 访问nginx默认网页

centos 7默认80端口未对外开放，需要设置防火墙80端口对外开放
    
开启80对外端口

    sudo firewall-cmd --permanent --query-port=80/tcp # 查看80端口是否对外开放
    sudo firewall-cmd --permanent --add-port=80/tcp # 设置80端口对外开放
    sudo firewall-cmd --reload # 重启防火墙
    
在浏览器输入机器IP就可以访问了

## 安装mysql ##

1.下载安装mysql

    wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
    sudo rpm -ivh mysql57-community-release-el7-9.noarch.rpm
    sudo yum install mysql-server

2.启动msql

    sudo systemctl start mysqld
    
查看启动状态

    sudo systemctl status mysqld

mysql安装过程会产生root的一个临时密码，如下命令查看

    sudo grep 'temporary password' /var/log/mysqld.log
    
执行该命令，产生如下输出
   
    2018-07-17T06:03:25.348251Z 1 [Note] A temporary password is generated for root@localhost: tpZw>+u1)vfK

3.配置mysql

mysql有一个安全脚本，改变一些不安全的缺省设置，执行如下命令启动安全设置

    sudo mysql_secure_installation

启动该命令后，会首先让你输入上文临时root密码，然后重新设置root密码，剩余选项，选择Yes就可以

4.测试mysql

输入命令：
    
    mysqladmin -u root -p version

命令执行后，如下输出：

    mysqladmin  Ver 8.42 Distrib 5.7.22, for Linux on x86_64
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Server version          5.7.22
    Protocol version        10
    Connection              Localhost via UNIX socket
    UNIX socket             /var/lib/mysql/mysql.sock
    Uptime:                 23 min 8 sec
    
    Threads: 1  Questions: 13  Slow queries: 0  Opens: 113  Flush tables: 1  Open tables: 106  Queries per second avg: 0.009

5.mysql客户端使用

输入命令

    mysql -u root -p
    mysql> show databases; // 显示数据库
    mysql> create database jed; // 创建数据库
    mysql> use jed; //访问数据库jed
    mysql> show tables; //查看数据表

6.mysql开发相关的安装包

    sudo yum install mysql-community-devel
    sudo yum -y install mysql-devel
    
## 安装postgresql ##

### 安装 ###

    sudo yum -y install postgresql-server postgresql-contrib

创建database cluster

    sudo postgresql-setup initdb

缺省情况下，PostgreSQL不能密码授权，可以编辑HBA(host-based authentication)配置，改为密码授权

    sudo vi /var/lib/pgsql/data/pg_hba.conf

在文件最后找到如下内容：

    host    all             all             127.0.0.1/32            ident
    host    all             all             ::1/128                 ident
    
把ident替换为md5

    host    all             all             127.0.0.1/32            md5
    host    all             all             ::1/128                 md5

启动，并设置开机启动PostgreSQL:

    sudo systemctl start postgresql
    sudo systemctl enable postgresql
    

https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-centos-7

## 安装java ##

    yum install java-1.8.0-openjdk
    yum install java-1.8.0-openjdk-devel

配置环境变量,vi /etc/profile 文件末尾添加如下内容

    export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.171-8.b10.el7_5.x86_64/
    export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$PATH:$JAVA_HOME/bin
    
然后执行如下命令

    source /etc/profile
    
让配置生效

    java -version # 查看java版本

## 安装scala ##

1.确保java版本1.8以上

    java -version

2.下载scala包

下载并解压文件

    wget https://downloads.lightbend.com/scala/2.12.6/scala-2.12.6.tgz
    tar -xzvf scala-2.12.6.tgz
    
重命名并移至/usr/local/share/

    mv scala-2.12.6 scala
    mv /download/scalapath /usr/local/share
    
修改环境变量,修改配置文件profile

    sudo vim /etc/profile

在文件的末尾加入

    export PATH=$PATH:/usr/local/share/scala/bin
    
然后保存，重启终端，执行Scala命令

使用了zsh终端的，可以在~/.zshrc文件末尾添加如上内容


## 安装sbt ##

下载sbt-1.1.6

    wget https://piccolo.link/sbt-1.1.6.tgz
    tar -xvzf sbt-1.1.6.tgz
    mv sbt /usr/local/sbt
    cd /usr/local/sbt
    vi sbt
    
添加如下内容

    #!/bin/bash
    SBT_OPTS="-Xms512M -Xmx1536M -Xss100M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=256M"
    java $SBT_OPTS -jar /usr/local/sbt/bin/sbt-launch.jar "$@"
    
修改sbt脚本权限

    sudo chmod a+x sbt

配置PATH环境

    vi /etc/profile
    export PATH=/usr/local/sbt/:$PATH # 在文件尾部添加
    
使sbt生效

    source /etc/profile

测试sbt是否安装成功

    sbt sbtVersion

