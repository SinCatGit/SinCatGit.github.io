---
layout: post
title: centos使用指南
author: sincat
date: 2018-07-17 14:41:15 +0800
category: 技术
tags: centos 

published: true
---

本文针对centos 7 版本的使用指南


## 防火墙 ##

查看已经开放的端口：

    firewall-cmd --list-ports

开启端口

    firewall-cmd --zone=public --add-port=80/tcp --permanent

命令含义：

    –zone #作用域

    –add-port=80/tcp #添加端口，格式为：端口/通讯协议

    –permanent #永久生效，没有此参数重启后失效


重启防火墙

    firewall-cmd --reload #重启firewall
    systemctl stop firewalld.service #停止firewall
    systemctl disable firewalld.service #禁止firewall开机启动
    firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）

