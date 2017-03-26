---
layout: post
title: javascript的apply、call、bind
subtitle: javascript apply
author: sincat
date: 2017-03-27 23:50:15 +0800
category: 技术
tags: javascript
published: true
---

## Apply用法
在"monkey-patching"中使用apply。Apply可以作为monkey-patch一个Firefox或JS库内建函数的最好方式。
对于someobject.foo 函数，你可以用一种旁门左道的方式来修改这个函数，像这样：

    var originalfoo = someobject.foo;
    someobject.foo = function() {
      //在调用函数前干些什么
      console.log(arguments);
      //像正常调用这个函数一样来进行调用：
      originalfoo.apply(this,arguments);
      //在这里做一些调用之后的事情。
    }

## call和apply
    
    obj.call(thisObj, arg1, arg2, ...);
    obj.apply(thisObj, [arg1, arg2, ...]);


两者作用一致，都是把obj(即this)绑定到thisObj，这时候thisObj具备了obj的属性和方法。
或者说thisObj『继承』了obj的属性和方法。绑定后会立即执行函数。唯一区别是apply接受的是数组参数，
call接受的是连续参数。


    function add(j, k){
        return j+k;
    }
    
    function sub(j, k){
        return j-k;
    }


在控制台运行：


    add(5,3); //8
    add.call(sub, 5, 3); //8
    add.apply(sub, [5, 3]); //8
    
    sub(5, 3); //2
    sub.call(add, 5, 3); //2
    sub.apply(add, [5, 3]); //2
    
## 调用原生对象的方法


    var a = {0:1, 1:"yjc", length: 2}; 
    
    a.slice(); //TypeError: a.slice is not a function
    
    Array.prototype.slice.call(a);//[1, "yjc"]
    
   
对象a类似array，但不具备array的slice等方法。使用call绑定，这时候就可以调用slice方法。

实现继承

## 实现对象继承

    var Parent = function(){
        this.name = "yjc";
        this.age = 22;
    }
    
    var child = {};
    
    console.log(child);//Object {} ,空对象
    
    Parent.call(child);
    
    console.log(child); //Object {name: "yjc", age: 22}
    
以上实现了对象的继承。

## bind的使用

    obj.bind(thisObj, arg1, arg2, ...);

把obj绑定到thisObj，这时候thisObj具备了obj的属性和方法。与call和apply不同的是，bind绑定后不会立即执行。

同样是add()和sub()：

    add.bind(sub, 5, 3); //不再返回8
    add.bind(sub, 5, 3)(); //8
    
如果bind的第一个参数是null或者undefined，等于将this绑定到全局对象。

## 实际前端开发中的应用

