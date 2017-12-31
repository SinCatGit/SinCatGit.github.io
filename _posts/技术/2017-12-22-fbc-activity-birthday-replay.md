---
layout: post
title: 12月22日突发流量技术复盘
subtitle: css
author: sincat
date: 2017-12-25 17:18:15 +0800
category: 技术
tags: 大流量
published: true
---

## 背景
12月22日是流量突然暴增。


## 技术调优

负载均衡

    1.阿里云设置TCP负载均衡，轮询模式;
    2.web服务器镜像，购置新的服务器，采用镜像加载系统；
    3.启动服务器后，添加ip白名单到数据库；
    4.添加服务器到负载均衡；
    5.负载均衡ip解析到访问域名；

静态文件CDN

    1.添加阿里云融合cdn
    2.添加加速域名
    3.选择图片小文件类型
    4.源站类型选择ip
    5.绑定加速域名的cname
    6.验证cdn是否生效
    
redis扩容

    1.redis设计不合理
    2.停止更新preload
    3.preload的加载id限制上线的id
    4.redis键值过期时间更短些

查看Nginx的access log
    
    1.sudo tail -f /var/log/nginx/access.log
    2.tail -f /var/log/nginx/access.log|awk '{print $9}'
    3.sudo tail -f /var/log/nginx/access.log|awk '{print $9}'
    4.查看499
    
celery Worker图片处理
    
    1.图片处理的celery worker压力太大
    2.提供降级服务，直接提供静态分享图片
    3.客户端canvas处理，兼容性是大问题
    4.服务端处理，响应速度是大问题
    5.采用服务端，提供优质服务
    6.这个解决分享页加载慢
    
sql慢查询语句
    
    1.查看阿里云Mysql的实时性能分析
    2.获取查询慢的语句
    3.优化语句
    4.暂停相关语句的执行
    5.这个解决下订单慢


Django框架的MemCache
    
    1.提供一些查询的缓存
    2.提高命中率
    
自动化部署
    
    1.使用fabric
    2.免密码输入

队列监控
    
    1.celery worker队列监控

服务器带宽扩容



    