---
title: ES6_Symbol
tags:
  - ES6
  - Javascript
date: 2018-05-06 15:15:45
---


### es6前javascript基本数据类型有6种：

    Undefined
    Null
    布尔值（Boolean）
    字符串（String）
    数值（Number）
    对象（Object）

ES6引入了一种新的原始数据类型Symbol, 表示独一无二的值。


### 用法：

    let s = Symbol();

    typeof s
    // "symbol"

Symbol函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述:

    let s1 = Symbol('foo');
    let s2 = Symbol('bar');

    s1 // Symbol(foo)
    s2 // Symbol(bar)

    s1 === s2 //false



Symbol 值不能与其他类型的值进行运算，只能转为字符串类型和布尔类型。    

    s1.toString() // "Symbol(foo)"
    s2.toString() // "Symbol(bar)"

    !s1 //false

### 应用：消除魔术字符串
魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替。

    // react中 action.js
    export const INCREMENT_COUNTER = 'INCREMENT_COUNTER';

    export function increment() {
      return {
        type: INCREMENT_COUNTER
      };
    }

    // reduce.js
    import { INCREMENT_COUNTER } from './action.js'
    switch(type) {
      case INCREMENT_COUNTER:
        //do something
        ...
    }

用Symbol：

    // react中 action.js
    export const INCREMENT_COUNTER = Symbol('INCREMENT_COUNTER');

    export function increment() {
      return {
        type: INCREMENT_COUNTER
      };
    }

    // reduce.js
    import { INCREMENT_COUNTER } from './action.js'
    switch(type) {
      case INCREMENT_COUNTER:
        //do something
        ...
    }


### 内置的 Symbol 值:
除了定义自己使用的 Symbol 值以外，ES6 还提供了 11 个内置的 Symbol 值，指向语言内部使用的方法：

    Symbol.iterator
    Symbol.hasInstance
    Symbol.isConcatSpreadable
    Symbol.species
    Symbol.match
    Symbol.replace
    Symbol.search
    Symbol.split  
