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

```
sudo apt install make
cd ~
git clone https://github.com/SinCatGit/jon.mac.git
cd jon.mac/vim && make

```

## 安装git

```
sudo apt-get install git
cd ~/jon.mac/git && make

```

## 安装oh-my-zsh
    

```
sudo apt install curl
sudo apt install zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```


## 安装tmux

```
sudo apt install tmux

```

```
cd ~/jon.mac/tmux && make
```

## 安装gdebi

gdebi相比于 GUI ，在终端下安装 deb 包可以获得更多的信息提示，但是使用 dpkg 命令又无法自动解决依赖问题，因此建议使用 gdebi 安装器替代以上两者。

```
sudo apt install gdebi
```

## 安装chrome浏览器

下载chrome google-chrome-stable_current_amd64.deb包

安装chrome包

```
sudo gdebi  google-chrome-stable_current_amd64.deb
```


## 安装docker

1. 更新包索引

```
$ sudo apt-get update
```


2. 允许apt通过HTTPS安装软件


```
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

3. 添加官方GPG key


```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```


通过查询最后8个字符，验证 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88


```
$ sudo apt-key fingerprint 0EBFCD88

pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

4. 设置stable repository


```
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

5. 更新apt包索引


```
$ sudo apt-get update
```

6. 安装最新Docker CE

```
sudo apt-get install docker-ce
```
安装该包的时候，肯能因为GFW访问不了。需要科学上网一下。



## 安装open jdk 8

查看 [https://openjdk.java.net/install](https://openjdk.java.net/install)
```
$ sudo apt-get install openjdk-8-jdk
```


## 安装scala

1. 下载安装 [https://www.scala-lang.org/download/](https://www.scala-lang.org/download/)

```
cd ~
wget https://downloads.lightbend.com/scala/2.12.8/scala-2.12.8.tgz
tar -xzvf scala-2.12.8.tgz
cd /usr/local/share/
mv ~/scala-2.12.8 scala
```

2. 配置环境变量
    
修改环境变量,修改配置文件profile

    sudo vim /etc/profile

在文件的末尾加入

    export PATH=$PATH:/usr/local/share/scala/bin
    
然后保存，重启终端，执行Scala命令

使用了zsh终端的，可以在~/.zshrc文件末尾添加如上内容

## 安装sbt

下载 [https://www.scala-sbt.org/download.html](https://www.scala-sbt.org/download.html)
```
wget https://piccolo.link/sbt-1.2.7.tgz
tar -xzvf sbt-1.2.7.tgz
mv sbt /usr/local/sbt
cd /usr/local/sbt
vim sbt
```

添加如下内容

```
#!/bin/bash
SBT_OPTS="-Xms512M -Xmx1536M -Xss100M -XX:+CMSClassUnloadingEnabled -XX:MaxPermSize=256M"
java $SBT_OPTS -jar /usr/local/sbt/bin/sbt-launch.jar "$@"
```

修改sbt脚本权限

    sudo chmod a+x sbt

配置PATH环境

```
vi /etc/profile
    export PATH=/usr/local/sbt/:$PATH # 在文件尾部添加
```

使sbt生效

```
source /etc/profile
```

测试sbt是否安装成功

```
sbt sbtVersion
```
