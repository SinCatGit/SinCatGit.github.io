---
layout: post
title: ubuntu 18.04程序猿使用指南
author: sincat
date: 2018-12-17 20:38:15 +0800
category: 技术
tags: ubuntu 

published: true
---

本文针对ubuntu 18.04的使用指南


## 更新源 ##

1. 备份源

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

2. 阿里源

    
```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

3. 修改源

替换该文件内容为“阿里源”
```
sudo gedit /etc/apt/sources.list  
```

4. 更新软件列表

upgrade 命令，会访问源列表里的每个网址，并读取软件列表，然后保存在本地电脑。

软件包管理器里看到的软件列表，都是通过update命令更新的。

```
sudo apt-get update
```

5. 更新软件

upgrade命令会把本地已安装的软件，与刚下载的软件列表里对应软件进行对比，如果发现已安装的软件版本太低，就会更新该软件。

```
sudo apt-get upgrade
```


## 安装vim

```
sudo apt-get install vim
```

## 安装git

```
sudo apt-get install git
```

