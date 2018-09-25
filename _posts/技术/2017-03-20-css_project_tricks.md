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

## 鼠标划过显示图片

```css

  .ims-popover{
    display: inline-block;
    position: relative;
  }
  .ims-popover-con{
    position: absolute;
    display: none;
    width: 10vw;
    height: 10vw;
    top:-10vw;
    left: -4vw;
  }
  .wechat-img{
    background: url("./wechat-popover.jpg");
    background-size: cover;
  }
  .ims-popover:hover .ims-popover-con{
    display: block;
  }

  .qq-img{
    background: url("./qq-popover.jpg");
    background-size: cover;
  }

```

```html
<div class="ims">
    <a class="iconfont ims-popover">
        &#xe610;
        <div class="ims-popover-con wechat-img"></div>
    </a>
    <a class="iconfont ims-popover">
    &#xe6af;
        <div class="ims-popover-con qq-img"></div>
    </a>
</div>
```
