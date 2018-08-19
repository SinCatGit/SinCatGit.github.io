---
layout: post
title: javascript模块引入
author: sincat
date: 2018-08-19 10:26:15 +0800
category: 技术
tags: javacript CommonJS requirejs ES6 

published: true
---

## CommonJS

    CommonJS是nodejs也就是服务器端广泛使用的模块化机制。 
    该规范的主要内容是，模块必须通过module.exports 导出对外的变量或接口，通过 require() 来导入其他模块的输出到当前模块作用域中。
    
根据这个规范，每个文件就是一个模块，有自己的作用域，文件中的变量、函数、类等都是对其他文件不可见的。

如果想在多个文件分享变量，必须定义为global对象的属性。（不推荐）

### 定义模块

在每个模块内部，module变量代表当前模块。它的exports属性是对外的接口，将模块的接口暴露出去。其他文件加载该模块，实际上就是读取module.exports变量。

    var x = 5;
    var addX = function (value) {
      return value + x;
    };
    module.exports.x = x;
    module.exports.addX = addX;
    
### 加载模块

require方法用于加载模块，后缀名默认为.js

    var app = require('./app.js');

模块加载的顺序，按照其在代码中出现的顺序

根据参数的不同格式，require命令去不同路径寻找模块文件。

### 入口文件

一般都会有一个主文件（入口文件），在index.html中加载这个入口文件，然后在这个入口文件中加载其他文件。

可以通过在package.json中配置main字段来指定入口文件。

### 模块缓存

第一次加载某个模块时，Node会缓存该模块。以后再加载该模块，就直接从缓存取出该模块的module.exports属性。

### 加载机制

CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。


## AMD

    AMD（异步模块定义）是为浏览器环境设计的，因为 CommonJS 模块系统是同步加载的，当前浏览器环境还没有准备好同步加载模块的条件。

requirejs即为遵循AMD规范的模块化工具。 

RequireJS的基本思想是，通过define方法，将代码定义为模块；通过require方法，实现代码的模块加载。

### 定义模块

define方法用于定义模块，RequireJS要求每个模块放在一个单独的文件里。

按照是否依赖其他模块，可以分成两种情况讨论。第一种情况是定义独立模块，即所定义的模块不依赖其他模块；第二种情况是定义非独立模块，即所定义的模块依赖于其他模块。


### 独立模块

    define(function(){
        ……
        return {
            //返回接口
        }
    })

define定义的模块可以返回任何值，不限于对象。

### 非独立模块

define(['module1','module2'],function(m1,m2){
    ……
    return {
        //返回接口
    }
})

要定义的模块依赖于module1和module2，那么第一个参数就是依赖的模块的数组。

第二个参数是一个函数，仅当依赖的模块都加载成功后才会被调用。此函数的参数m1，m2与前面数组中的依赖模块一一对应。

此模块必须返回一个对象，供其他模块调用。


### 加载模块

同样使用require（）方法来加载模块，但由于是异步的，因此使用回调函数的形式。

    require(['foo','bar'],function(foo,bar){
        ……
    })

上面方法表示加载foo和bar两个模块，当这两个模块都加载成功后，执行一个回调函数。该回调函数就用来完成具体的任务。

require方法也可以用在define方法内部。

define(function(require){
     var otherModule = require('otherModule');
})

require方法允许添加第三个参数，即错误处理的回调函数。

    require(
        [ "backbone" ], 
        function ( Backbone ) {
            return Backbone.View.extend({ /* ... */ });
        }, 
        function (err) {
            // ...
        }
    );
    
### 配置

require方法本身也是一个对象，它带有一个config方法，用来配置require.js运行参数。

    require.config({
        paths: {
            jquery:'lib/jquery'
        }
    });

paths：paths参数指定各个模块的位置。这个位置可以是同一个服务器上的相对位置，也可以是外部网址。可以为每个模块定义多个位置，如果第一个位置加载失败，则加载第二个位置。上面就是指定了jquery的位置，那么就可以直接在文件中require（['jquery'],function($){}）

shim：有些库不是AMD兼容的，这时就需要指定shim属性的值。shim可以理解成“垫片”，用来帮助require.js**加载非AMD规范的库**。

    require.config({
        paths: {
            "backbone": "vendor/backbone",
            "underscore": "vendor/underscore"
        },
        shim: {
            "backbone": {
                deps: [ "underscore" ],
                exports: "Backbone"
            },
            "underscore": {
                exports: "_"
            }
        }
    });

### 使用

在主页面index.html中先通过script标签引入require.min.js。 
再通过script标签引入一个入口文件main.js，此入口文件一般用于配置（require.config），以及引入其他模块。

## CommonJS与AMD

CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作。

AMD规范则是异步加载模块，允许指定回调函数，在回调函数中执行操作。
由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。

AMD规范允许输出的模块兼容CommonJS规范，这时define方法需要写成下面这样：

    define(function(require,exports,module){
        var someModule = require("someModule");
        var anotherModule = require("anotherModule");
        ……
        exports.asplode = function(){
            // ...
        }
    })

## ES6 Modules

ES6正式提出了内置的模块化语法，我们在浏览器端无需额外引入requirejs来进行模块化。

ES6中的模块有以下特点：

- 模块自动运行在严格模式下
- 在模块的顶级作用域创建的变量，不会被自动添加到共享的全局作用域，它们只会在模块顶级作用域的内部存在；
- 模块顶级作用域的 this 值为 undefined
- 对于需要让模块外部代码访问的内容，模块必须导出它们

### 定义模块

使用export关键字将任意变量、函数或者类公开给其他模块。


```
    //导出变量
    export var color = "red";
    export let name = "cz";
    export const age = 25;
    
    //导出函数
    export function add(num1,num2){
        return num1+num2;
    }
    
    //导出类
    export class Rectangle {
        constructor(length, width) {
            this.length = length;
            this.width = width;
        }
    }
    
    function multiply(num1, num2) {
        return num1 * num2;
    }
    
    //导出对象，即导出引用
    export {multiply}
```

### 重命名模块

重命名想导出的变量、函数或类的名称

```
    function sum(num1, num2) {
        return num1 + num2;
    }
    
    export {sum as add}
```

这里将本地的sum函数重命名为add导出，因此在使用此模块的时候必须使用add这个名称。

### 导出默认值

模块的默认值是使用 default 关键字所指定的单个变量、函数或类，而你在每个模块中只能设置一个默认导出。

```
export default function(num1, num2) {
    return num1 + num2;
}
```

此模块将一个函数作为默认值进行了导出， default 关键字标明了这是一个默认导出。此函数并不需要有名称，因为它就代表这个模块自身。对比最前面使用export导出的函数，并不是匿名函数而是必须有一个名称用于加载模块的时候使用，但是默认导出则无需一个名字，因为模块名就代表了这个导出值。

也可以使用重命名语法来导出默认值。

```
function sum(num1, num2) {
    return num1 + num2;
}

export { sum as default };
```

### 加载模块

在模块中使用import关键字来导入其他模块。 
import 语句有两个部分，一是需要导入的标识符，二是需导入的标识符的来源模块。此处是导入语句的基本形式：

``` 
import { identifier1,identifier2 } from "./example.js"

```

大括号中指定了从给定模块导入的标识符
from指明了需要导入的模块。模块由一个表示模块路径的字符串来指定。
当从模块导入了一个绑定时，你不能在当前文件中再定义另一个同名变量（包括导入另一个同名绑定），也不能在对应的 import 语句之前使用此标识符，更不能修改它的值。

### 导入单个绑定

如果一个模块只导出了一个函数（或变量或类），或者导出了多个接口但是只选择导入其中的一个，那么就可以写成下面单个导入的模式：

```
import {sum} from './example.js'
```

### 导入多个绑定

从一个模块中导入多个绑定：

```
import {sum,multiply} from './example.js'
```

### 完全导入一个模块

还有一种情况，就是将整个模块当做单一对象导入，该模块的所有导出都会作为对象的属性存在：

```
import * as example from './example.js'
example.sum(1,2);
example.multiply(2,3);
```

在此代码中， example.js 中所有导出的绑定都被加载到一个名为 example 的对象中，具名导出（ sum() 函数、 multiple() 函数）都成为 example 的可用属性。 

这种导入格式被称为命名空间导入，这是因为该 example 对象并不存在于 example.js 文件中，而是作为一个命名空间对象被创建使用，其中包含了 example.js 的所有导出成员。

然而要记住，无论你对同一个模块使用了多少次 import 语句，该模块都只会被执行一次。

在导出模块的代码执行之后，已被实例化的模块就被保留在内存中，并随时都能被其他 import 所引用.

```
import { sum } from "./example.js";
import { multiply } from "./example.js";
import { magicNumber } from "./example.js";
```

尽管此处的模块使用了三个 import 语句，但 example.js 只会被执行一次。若同一个应用中的其他模块打算从 example.js 导入绑定，则那些模块都会使用这段代码中所用的同一个模块实例。

### 重命名导入

与导出相同，我们同样可以重命名导入的绑定：

```
import { sum as add} from './example.js'
```

### 导入默认值

如果一个模块导出了默认值，那么可以这样导入默认值：

```
import sum from "./example.js";
```

这个导入语句从 example.js 模块导入了其默认值。注意此处并未使用花括号，与之前在非默认的导入中看到的不同。本地名称 sum 被用于代表目标模块所默认导出的函数，因此无需使用花括号。

如果一个模块既导出了默认值、又导出了一个或更多非默认的绑定的模块：

```
export let color = "red";

export default function(num1, num2) {
    return num1 + num2;
}
```

可以像下面这样使用一条import语句来导入它的所有导出绑定：

```
import sum,{color} from "./example.js"
```

逗号将默认的本地名称与非默认的名称分隔开，后者仍旧被花括号所包裹。 
要记住在 import 语句中默认名称必须位于非默认名称之前。

### 导入的再导出

有时想在当前的模块中将已导入的内容再导出去，可以像下面这样写：

```
import {sum} from './example.js'
……
export {sum}
```

但是有一种更简洁的方法：

```
export {sum} from './example.js'
```

同样可以重命名：

```
export { sum as add } from "./example.js";
```
也可以使用完全导出：

```
export * from "./example.js";
```

### 限制

export 与 import 都有一个重要的限制，那就是它们必须被用在其他语句或表达式的外部，而不能使用在if等代码块内部。原因之一是模块语法需要让 JS 能静态判断需要导出什么，正因为此，你只能在模块的顶级作用域使用 export与import。