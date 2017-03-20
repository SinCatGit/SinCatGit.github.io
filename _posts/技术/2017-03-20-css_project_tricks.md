---
layout: post
title: css开发工程实践技巧
subtitle: css
author: sincat
date: 2017-03-20 10:37:15 +0800
category: 技术
tags: css
published: true
---

## 扩大点击区域
    
    .heart{
        position: relative;
        
    }
    .heart:after{
        position:absolute;
        left:-10px;
        top:-10px;
        right:-10px;
        bottom:-10px;
    }


