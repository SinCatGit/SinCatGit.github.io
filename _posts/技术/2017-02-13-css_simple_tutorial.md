---
layout: post
title: css简明教程
subtitle: css
author: sincat
date: 2017-02-13 18:15:15 +0800
category: 技术
tags: css
published: true
---

## 选择器
    
1.标签选择器
    
2.id选择器

3.class选择器

4.属性选择器

5.组合选择器

6.伪类、伪元素


## 盒模型
    
    CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距(margin)，边框(border)，填充(padding)，和实际内容(content)。
    
    盒模型默认width是不包括border、padding的宽度，实际编程中添加box-sizing:border-box,width就会包括border及padding的宽度。
    
    最佳实践，默认所有都是border-box类型盒模型。
    * {
        -webkit-box-sizing: border-box;
        -moz-box-sizing: border-box;
        box-sizing: border-box;
    }
    
## display属性

1. block
    
2. inline

3. none


    一些特殊元素的默认 display 值是它，例如 script 。 display:none 通常被 JavaScript 用来在不删除元素的情况下隐藏或显示元素。
    
    它和 visibility 属性不一样。把 display 设置成 none 元素不会占据它本来应该显示的空间，但是设置成 visibility: hidden; 还会占据空间。
    
4. inline-block


## 定位
    
1.static

2.fixed

3.relative

4.absolute

    absolute 是最棘手的position值。 absolute 与 fixed 的表现类似，但是它不是相对于视窗而是相对于最近的“positioned”祖先元素。
    如果绝对定位（position属性的值为absolute）的元素没有“positioned”祖先元素，那么它是相对于文档的 body 元素，并且它会随着页面滚动而移动。
    记住一个“positioned”元素是指 position 值不是 static 的元素。
    
5.float

## 响应设计

1.viewport

    添加<head>标签添加如下内容，适配响应手机
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    参考 http://www.runoob.com/css/css-rwd-viewport.html

2.媒体查询


## display使用flexbox

参考 http://www.ruanyifeng.com/blog/2015/07/flex-examples.html



## 参考网站

1. http://zh.learnlayout.com/toc.html

2. https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Getting_started

3. https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Getting_started


