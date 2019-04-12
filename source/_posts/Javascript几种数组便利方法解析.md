---
layout: source
title: Javascript几种数组便利方法解析
date: 2019-03-18 16:06:39
tags:
---


这篇文章将详细介绍以下 4 种循环语法的区别：

#### for

#### forEach

#### for/in

#### for/of


### 语法

使用for和for/in，我们可以访问数组的下标，而不是实际的数组元素值：

```
for (let i = 0; i < arr.length; ++i) {
    console.log(arr[i]);
}

for (let i in arr) {
    console.log(arr[i]);
}
```

使用for/of，则可以直接访问数组的元素值：

```
for (const v of arr) {
    console.log(v);
}
```

使用forEach()，则可以同时访问数组的下标与元素值：

```
arr.forEach((v, i) => console.log(v));
```

### 非数字属性

JavaScript 的数组就是 Object，这就意味着我们可以给数组添加字符串属性：

```
const arr = ["a", "b", "c"];

typeof arr; // 'object'

arr.test = "bad"; // 添加非数字属性

arr.test; // 'abc'
arr[1] === arr["1"]; // true, JavaScript数组只是特殊的Object
```

4 种循环语法，只有for/in不会忽略非数字属性：

```
const arr = ["a", "b", "c"];
arr.test = "bad";

for (let i in arr) {
    console.log(arr[i]); // 打印"a, b, c, bad"
}
```

正因为如此，[使用for/in便利数组并不好](https://stackoverflow.com/questions/500504/why-is-using-for-in-with-array-iteration-a-bad-idea)

要点： 避免使用for/in来遍历数组，除非你真的要想要遍历非数字属性。可以使用 ESLint 的[guard-for-in](https://eslint.org/docs/rules/guard-for-in)规则来禁止使用for/in。

### 数组的空元素

JavaScript 数组可以有空元素。以下代码语法是正确的，且数组长度为 3

```
const arr = ["a", , "c"];

arr.length; // 3
```

让人更加不解的一点是，循环语句处理['a',, 'c']与['a', undefined, 'c']的方式并不相同。

对于['a',, 'c']，for/in与forEach会跳过空元素，而for与for/of则不会跳过：

```
// 打印"a, undefined, c"
for (let i = 0; i < arr.length; ++i) {
    console.log(arr[i]);
}

// 打印"a, c"
arr.forEach(v => console.log(v));

// 打印"a, c"
for (let i in arr) {
    console.log(arr[i]);
}

// 打印"a, undefined, c"
for (const v of arr) {
    console.log(v);
}
```

对于['a', undefined, 'c']，4 种循环语法一致，打印的都是”a, undefined, c”。

还有一种添加空元素的方式：

```
// 等价于`['a', 'b', 'c',, 'e']`
const arr = ["a", "b", "c"];
arr[5] = "e";
```

要点： for/in与forEach会跳过空元素，数组中的空元素被称为“holes”。


* 原文：[For vs forEach() vs for/in vs for/of in JavaScript](http://thecodebarbarian.com/for-vs-for-each-vs-for-in-vs-for-of-in-javascript.html)
* 译者: [Fundebug](https://www.fundebug.com/)

