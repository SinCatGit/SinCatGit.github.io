---
layout: post
title: centos安装shadowsocks
author: sincat
date: 2019-02-11 12:34:15 +0800
category: 技术
tags: shadowsocks 

published: false
---

1. 安装pip

```
# curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
# python get-pip.py
```


2. 安装shadowsocks

```
# pip install shadowsocks
```

3. 配置shadowsocks
``` 
# vi /etc/shadowsocks.json

{
  "server":"0.0.0.0",
  "port_password":{
    "7381":"7381password",
    "7382":"7382password"
  },
  "timeout":300,
  "method":"aes-256-cfb",
  "fast_open":false,
  "workers": 2
}

```

4. 开启端口

```
# firewall-cmd --zone=public --add-port=7381/tcp --permanent
# firewall-cmd --zone=public --add-port=7382/tcp --permanent
# firewall-cmd --reload
```

5.启动shadowsocks

```
ssserver -c /etc/shadowsocks.json -d start
```
