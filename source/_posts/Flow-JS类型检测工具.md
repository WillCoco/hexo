---
title: Flow-JS类型检测工具
date: 2017-12-20 23:39:11
tags: [javascript, react]
---
### 前言
Facebook超爱Javascript；它快，表达性好，而且到处运行，是构建产品的极佳语言。同时，因为没有静态类型让开发者困扰。Bug难以发现（比如，崩溃的原因隐藏很深），代码维护犹如噩梦（比如，在不知道所有依赖的情况下进行重构风险很大）。Flow改进了速度和效率促进了开发者在使用Javascript的生成效率。

Flow的类型检查是选择性的 —— 你不需要一次性执行检查所有。然而，Flow背后的设计基于假定大多数Javascript的代码类型是隐式静态类型；虽然类型可能不会到处在代码中出现，它们是以一种可以按照代码正确性推理出来的形式存在于开发者的思路中。一旦可能，Flow就去推断这些类型，意味着它可以不需要改动代码就能发现类型错误。另一发面，一些如存在于框架中的Javascript代码，大量使用了反射使得静态类型推断非常困难。对于这种天然动态的代码，类型检查就会错漏百出，因此Flow提供对此类代码添加信任并继续。这种设计在Facebook内部被大量的Javascript代码库所验证：大多数代码没有通过隐式静态类型检查条目，这些条目让开发者可以不用添加注释就能检查代码类型错误。

这使得Flow从根本上区别于其他Javascript的类型系统（如TypeScript），通过弱化的假设大多数JavaScript代码是动态输入的，并由开发者自己表达哪些代码应该是静态类型。通常来看，这类设计会导致检查覆盖率降低：更少的类型错误被检测到，工具不够高效。然而对于某些情况下是合理的，一般这种设计如果没有通过大量额外的努力就无法对实际开发提供足够多的帮助。尽管如此，Flow让你可以简单就获得这种弱化的类型检查，对于现有代码非常有用。

如何对一个文件进行类型检查？

如果你想对一个文件进行类型检查，必须在文件头部加上一行注释；

    // @flow
      或者
    /* @flow */

意思简单明了，AT 一下 Flow 检查器，意思是：“嘿， Flow，来验一下我”。
当我们写完代码后，运行 flow check（或者配置的相应 npm 命令），Flow 会对加上了这行注释的 JS 文件进行类型检查，并且只会对加了这行注释的 JS 文件进行类型检查；这样可以方便的使你渐进式的把你项目中 JS 文件一个个的改写得符合规范。


### 自动的类型推导

在我们将一个 JS 文件加入类型检测的范围之后，运行 flow check, Flow 会怎么对这些文件进行操作呢？Flow 有一个自动的类型推导机制，很多情况下，Flow 都自动推导出变量的类型并持续跟踪其使用；比如：
let name = 'zhihu';
将变量 name 定义为 zhihu ，Flow 会自动推导 name 为 string 类型；如果后面的代码对 name 进行了不适用于字符串的操作，运行 Flow 命令对代码进行检测就会报错。比如：

    // @flow
    let name = ‘zhihu’
    console.log(name - 1) // 即便没有手动给 name 加上类型标注，Flow 也会在这里报错

### 基本类型的类型标注语法

JavaScript 中的基本类型，类型标注语法是在变量后加上一个冒号，空格，然后是相应的类型名称，如：

    // @flow
    const a: string = 'zhihu';
    const b: number = 5;
    const c: boolean = false;
    const d: void = undefined;
    const e: null = null;

以上需要注意的几点：
1. undefined 的类型是 void；
2. null 的类型是 null；
3. string 类型、number 类型和 boolean 类型，其类型名称都是小写开头；但是在 JS 中还有相对应的大写开头的类型名称，如 String，Number, Boolean；

在 Flow 中，大写开头的类型名和小写开头的类型名是有区别的。

    // @flow 
    const a: string = 'a';             // 字面量值对应的类型名称是小写
    const b: string = String('b');     // String 函数是将参数转化成一个字符串，仍然是小写的 string 类型
    const c: String = new String(‘c’); // 大写开头的类型名称，其对应的值是 new 创建出来的类型实例；

字面量值作为一种类型
在 Flow 中，字面量值也可以作为一种类型，符合这种类型的变量只有这个字面量本身；给这个变量赋其他值， Flow 在进行类型检测时都会报错；比如下例所示。

    // @flow
    let monthsAYear: 12 = 12;
    monthsAYear = 13; // Flow 会在这里报错

### 函数类型标注

对函数类型我们主要是标注其接受的参数类型和返回值类型；下面的示例中分别展示了如何对函数声明、函数表达式，以及箭头函数加上类型标注。

    // @flow 
    // 函数声明
    function getLength(str: string): number {
     return str.length;
    }
    // 函数表达式
    const greeting = function(welcome: string): void{
     console.log(welcome);
    }
    // 箭头函数
    const addNumber = (a: number, b: number): number => (a + b);

### 数组类型标注
对数组的标注 Flow 提供两种语法，
1. Array 后接一对尖括号，且尖括号里边就是数组项的类型名称；
2. 类型名称后加一对中括号。

    // @flow
    const names: Array<string> = ['a', 'b', 'c'];
    const ages: number[] = [1, 2, 3, 4];

### 元组（Tuple）类型的标注    

另外一种常见的数组是元组（Tuple）。在其他语言里，元组可能是一个特定的类型结构；但是在 JS 里，元组就是数组来表示的，并且是一个有限数组，数组每一项的类型分别标注出来；通常的使用场景比如一个用来表示数据库纪录的数组，比如函数返回多个返回值。

    // @flow
    const recordItem : [number, string, boolean] = [1, 'First', true];

一个数组被标注为元组类型后，每一项的类型都不可再次改变，且数组的长度也不能改变；因此对元组类型的数组进行 push、pop 操作，都是 Flow 不允许的。

### 对象类型的标注

对对象类型加上类型标注的方式是定义其结构（Shape），即有哪些属性，属性及属性值的类型；

    // @flow
    const borderConfig : {
     width: number,
     color: string,
     hasShadow: boolean
    } = {
     width: 10,
     color: 'red',
     hasShadow: true,
    }

上面这种写法非常不直观，类型定义内容跟对象本身混在一起；优化的方式是像下面这样将类型定义和类型标注分开。

    // @flow
    type BorderConfigType = {
     width: number,
     color: string,
     hasShadow: boolean
    }

    const borderConfig : BorderConfigType = {
     width: 10,
     color: 'red',
     hasShadow: true,
    } 

type 是 Flow 中的关键字，用来定义自定义的类型，并且可以在后面的类型标注中使用。例如：

    // @flow
    type StringType = string;
    const name: StringType = ‘zhihu’;

    type TupleType = [ number, string ]
    const record: TupleType = [ 1, ‘a’ ]

更多技术细节请参看：[官方文档](https://flow.org/en/docs/) / [中文翻译](https://zhenyong.github.io/flowtype/docs/builtins.html)

文章来源参考：
https://segmentfault.com/a/1190000004174468
https://zhuanlan.zhihu.com/p/26204569
