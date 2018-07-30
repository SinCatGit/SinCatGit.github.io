---
layout: post
title: 搭建yz测试机器
author: sincat
date: 2018-07-24 19:06:15 +0800
category: 技术
tags: test work

published: true
---

1. 安装jdk1.8

    yum install java-1.8.0-openjdk
    yum install java-1.8.0-openjdk-devel
    
2. 安装sbt

下载sbt-1.1.6

    wget https://piccolo.link/sbt-1.1.6.tgz
    tar -xvzf sbt-1.1.6.tgz
    mv sbt-1.1.6 /usr/local/sbt
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

3. 安装kafka

下载kafka到/var/www/目录

    tar -xvzf kafka_2.12-1.1.0.tgz # 解压缩
    cd kafka_2.12-1.1.0
    
启动server

    bin/zookeeper-server-start.sh config/zookeeper.properties # zookeeper
    bin/kafka-server-start.sh config/server.properties # kafka server

4. 安装postgresql

安装

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
    
初次安装后，默认生成一个名为postgres的数据库和一个名为postgres的数据库用户。这里需要注意的是，同时还生成了一个名为postgres的Linux系统用户。

    sudo su - postgres

进入postgres用户终端

    -bash-4.2$ cd yz_studio/tools/software/postgres/agent # 切换到agent目录
    -bash-4.2$ psql -f init.sql # 初始化agent端数据库


5.安装redis

安装redis

    wget http://download.redis.io/releases/redis-4.0.10.tar.gz
    tar xzf redis-4.0.10.tar.gz
    cd redis-4.0.10
    make
    
二进制包在src目录下

    ./src/redis-server # 启动redis
    ./src/redis-server -port 6380 # 启动redis 到端口 6380


6.安装node

下载安装

    curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
    sudo yum -y install nodejs
    
验证版本

    node -v  # v8.11.3

7.前端工程

安装vue-cli

    npm install -g vue-cli
    
clone前端工程
    
    git clone https://gitlab.com/jon8798/agent_fe.git
    cd agent_fe
    npm install
    npm run dev
   
    
 


