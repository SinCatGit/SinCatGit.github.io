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

关闭端口

    firewall-cmd --remove-port=5432/tcp --permanent

重启防火墙

    firewall-cmd --reload #重启firewall
    systemctl stop firewalld.service #停止firewall
    systemctl disable firewalld.service #禁止firewall开机启动
    firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
    

## 磁盘使用 ##

1.查看当前目录

    df -h
    
2.查看指定目录

    df -h /usr/

3.查看当前目录文件夹情况

    du --max-depth=1 -h 

4.查看指定目录

    du --max-depth=1 -h  /usr/

5.计算文件夹大小

    du -sh /usr/
    
## systemctl系统管理命令 ##

systemctl是Systemd的一个主命令。Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面。

    systemctl start cups.service # 启动电源管理服务 
    systemctl stop cups.service # 停止电源管理服务 
    systemctl status cups.service # 查看电源管理状态
    systemctl enable cups.service # 启动电源管理开机启动 
    systemctl disable cups.service # 停止电源管理开机启动
    
    
## 修改用户密码

passwd root

     
   
    
    
    
    


