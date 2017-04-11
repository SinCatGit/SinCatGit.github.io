---
layout: post
title: git基本使用
subtitle: javascript basics
author: sincat
date: 2017-04-11 11:42:15 +0800
category: 技术
tags: git
published: true
---

## 放弃本地修改，强制更新为远程修改
    
    git fetch --all  # 下载远程库内容，不做任何合并
    git reset --hard origin/master  # 把HEAD指向下载最新的版本
