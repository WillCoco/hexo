---
title: ES6_module模块化
tags:
  - ES6
  - Javascript
date: 2018-05-06 17:35:46
---


## 前言
随着网站逐渐变成"互联网应用程序"，嵌入网页的Javascript代码越来越庞大，网页越来越像桌面程序，越来越复杂。
Javascript模块化编程，已经成为一个迫切的需求。理想情况下，开发者只需要实现核心的业务逻辑，其他都可以加载别人已经写好的模块。
Javascript社区做了很多努力，在现有的运行环境中，实现"模块"的效果。


### 原始写法：
模块就是实现特定功能的一组方法。只要把不同的函数（以及记录状态的变量）简单地放在一起，就算是一个模块。

    function m1(){
     //...
    }

    function m2(){
     //...
    }

缺点很明显："污染"了全局变量，无法保证不与其他模块发生变量名冲突


### 对象写法

    var module1 = new Object({
      count : 0,
      m1 : function (){
        　//...
        },
      m2 : function (){
        //...
      }
    });

缺点：暴露所有模块成员，内部状态可以被外部改写

### 立即执行函数

    var module1 = (function(){
      var count = 0;
      var m1 = function(){
        //...
      };
      var m2 = function(){
      　//...
      };
      return {
      　m1 : m1,
      　m2 : m2
      };
    })();



### 模块化规范
在 ES6 之前，社区制定了一些模块加载方案：

    CommonJS
    AMD => require.js
    CMD => sea.js

ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代 CommonJS 和 AMD 规范，成为浏览器和服务器通用的模块解决方案。

### exs import/export 和传统requre/export区别
ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性。

    // CommonJS模块
    let { stat, exists, readFile } = require('fs');

    // 等同于
    let fs = require('fs');
    let stat = fs.stat;
    let exists = fs.exists;
    let readfile = fs.readfile;

上面代码的实质是整体加载fs模块（即加载fs的所有方法），生成一个对象（fs），然后再从这个对象上面读取 3 个方法。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”

ES6 模块不是对象，而是通过export命令显式指定输出的代码，再通过import命令输入。

    // ES6模块
    import { stat, exists, readFile } from 'fs';

上面代码的实质是从fs模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。

由于 ES6 模块是编译时加载，使得静态分析成为可能。有了它，就能进一步拓宽 JavaScript 的语法，比如flow 类型检查。

require随处可用，参数可拼接， import必须前置，静态字符串。

require输出的是一个值的拷贝，import 模块输出的是值的引用。

### import/export用法

    import fs from 'fs'
    import {default as fs} from 'fs'
    import * as fs from 'fs'
    import {readFile} from 'fs'
    import {readFile as read} from 'fs'
    import fs, {readFile} from 'fs'

    export default fs
    export const fs
    export function readFile
    export {readFile, read}
    export * from 'fs'
    module.exports = fs

　　　　
　　　　　　
　　　
　　　　
