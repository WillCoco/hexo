---
title: Lodash-练习记录-Array-1
tags:
  - Lodash
  - Javascript
categories: technology
comments: true
date: 2018-12-29 13:14:21
---

## Array

### _.chunk(array, [size=1])
#### 将数组按长度分城若干组

    _.chunk(['a', 'b', 'c', 'd'], 2);
    // => [['a', 'b'], ['c', 'd']]
     
    _.chunk(['a', 'b', 'c', 'd'], 3);
    // => [['a', 'b', 'c'], ['d']]
        

### __.compact(array)
#### 去除数组中转换bool为false的项

    _.compact([0, 1, false, 2, '', 3]);
    // => [1, 2, 3]        

### _.concat(array, [values])
#### 拼接数组，可同时拼接多个，拼接项可以不是数组

    var array = [1];
    var other = _.concat(array, 2, [3], [[4]]);
     
    console.log(other);
    // => [1, 2, 3, [4]]
     
    console.log(array);
    // => [1]      

### _.difference(array, [values])
#### array中不包含[values]中的项

    _.difference([2, 1], [2, 3]);
    // => [1]       

### _.differenceBy(array, [values], [iteratee=_.identity])
#### 类似difference，比较前array和[values]中的每一项都经过[iteratee=_.identity]处理

    _.differenceBy([2.1, 1.2], [2.3, 3.4], Math.floor);
    // => [1.2]
     
    // The `_.property` iteratee shorthand.
    _.differenceBy([{ 'x': 2 }, { 'x': 1 }], [{ 'x': 1 }], 'x');
    // => [{ 'x': 2 }]     

### _.differenceWith(array, [values], [comparator])
#### 类似difference，array和[values]的每一项传给比较函数[comparator]， 保留返回false的项

    var objects = [{ 'x': 1, 'y': 2 }, { 'x': 2, 'y': 1 }];
     
    _.differenceWith(objects, [{ 'x': 1, 'y': 2 }], _.isEqual);
    // => [{ 'x': 2, 'y': 1 }]     

### _.drop(array, [n=1])
#### 从左边开始位置切除n项

    _.drop([1, 2, 3]);
    // => [2, 3]
     
    _.drop([1, 2, 3], 2);
    // => [3]
     
    _.drop([1, 2, 3], 5);
    // => []
     
    _.drop([1, 2, 3], 0);
    // => [1, 2, 3]

### _.dropRight(array, [n=1])
#### 类似drop，从右边开始

    _.dropRight([1, 2, 3]);
    // => [1, 2]
     
    _.dropRight([1, 2, 3], 2);
    // => [1]
     
    _.dropRight([1, 2, 3], 5);
    // => []
     
    _.dropRight([1, 2, 3], 0);
    // => [1, 2, 3]

### _.dropRightWhile(array, [predicate=_.identity])
#### 从右边开始，切除满足条件的项

    var users = [
      { 'user': 'barney',  'active': true },
      { 'user': 'fred',    'active': false },
      { 'user': 'pebbles', 'active': false }
    ];

    _.dropRightWhile(users, function(o) { return !o.active; });
    // => objects for ['barney']
     
    // The `_.matches` iteratee shorthand.
    _.dropRightWhile(users, { 'user': 'pebbles', 'active': false });
    // => objects for ['barney', 'fred']
     
    // The `_.matchesProperty` iteratee shorthand.
    _.dropRightWhile(users, ['active', false]);
    // => objects for ['barney']
     
    // The `_.property` iteratee shorthand.
    _.dropRightWhile(users, 'active');
    // => objects for ['barney', 'fred', 'pebbles']

### _.dropWhile(array, [predicate=_.identity])
#### 从左边开始，切除满足条件的项


### _.fill(array, value, [start=0], [end=array.length])
#### 在start-end区间内为array填充值

    var array = [1, 2, 3];
     
    _.fill(array, 'a');
    console.log(array);
    // => ['a', 'a', 'a']
     
    _.fill(Array(3), 2);
    // => [2, 2, 2]
     
    _.fill([4, 6, 8, 10], '*', 1, 3);
    // => [4, '*', '*', 10]

### _.findIndex(array, [predicate=_.identity], [fromIndex=0])
#### 从fromIndex开始，从左往右，返回第一个满足条件[predicate=_.identity]的索引index

    var users = [
      { 'user': 'barney',  'active': false },
      { 'user': 'fred',    'active': false },
      { 'user': 'pebbles', 'active': true }
    ];
     
    _.findIndex(users, function(o) { return o.user == 'barney'; });
    // => 0
     
    // The `_.matches` iteratee shorthand.
    _.findIndex(users, { 'user': 'fred', 'active': false });
    // => 1
     
    // The `_.matchesProperty` iteratee shorthand.
    _.findIndex(users, ['active', false]);
    // => 0
     
    // The `_.property` iteratee shorthand.
    _.findIndex(users, 'active');
    // => 2

### _.findLastIndex(array, [predicate=_.identity], [fromIndex=array.length-1])
#### 从fromIndex开始，从右往左，返回第一个满足条件[predicate=_.identity]的索引index


### _.head(array)
#### 取array的第一个元素

    _.head([1, 2, 3]);
    // => 1
     
    _.head([]);
    // => undefined

### _.tail(array)
#### 除去取array的第一个元素

    _.tail([1, 2, 3]);
    // => [2, 3]
    
### _.last(array)
#### 取array的最后一个元素

    _.last([1, 2, 3]);
    // => 3

### _.flatten(array)
#### 展平一次数组

    _.flatten([1, [2, [3, [4]], 5]]);
    // => [1, 2, [3, [4]], 5]

### _.flattenDeep(array)
#### 展平数组

    _.flattenDeep([1, [2, [3, [4]], 5]]);
    // => [1, 2, 3, 4, 5]

### _.flattenDepth(array, [depth=1])
#### 展平n次数组

    var array = [1, [2, [3, [4]], 5]];
     
    _.flattenDepth(array, 1);
    // => [1, 2, [3, [4]], 5]
     
    _.flattenDepth(array, 2);
    // => [1, 2, 3, [4], 5]

### _.pull(array, [values])
#### array中除去values

    var array = ['a', 'b', 'c', 'a', 'b', 'c'];
     
    _.pull(array, 'a', 'c');
    console.log(array);
    // => ['b', 'b']

### _.pullAll(array, values)
#### 类似pull，values参数为数组

    var array = ['a', 'b', 'c', 'a', 'b', 'c'];
     
    _.pull(array, 'a', 'c');
    console.log(array);
    // => ['b', 'b']
    
### _.pullAllBy(array, values, [iteratee=_.identity])
#### 类似pull，values参数为数组

    var array = ['a', 'b', 'c', 'a', 'b', 'c'];
     
    _.pull(array, 'a', 'c');
    console.log(array);
    // => ['b', 'b']
    
### _.pullAllBy(array, values, [iteratee=_.identity])
#### 

    var array = [{ 'x': 1 }, { 'x': 2 }, { 'x': 3 }, { 'x': 1 }];
     
    _.pullAllBy(array, [{ 'x': 1 }, { 'x': 3 }], 'x');
    console.log(array);
    // => [{ 'x': 2 }]
    
### _.pullAllWith(array, values, [comparator])

    var array = [{ 'x': 1, 'y': 2 }, { 'x': 3, 'y': 4 }, { 'x': 5, 'y': 6 }];
     
    _.pullAllWith(array, [{ 'x': 3, 'y': 4 }], _.isEqual);
    console.log(array);
    // => [{ 'x': 1, 'y': 2 }, { 'x': 5, 'y': 6 }]
    
### _.pullAt(array, [indexes])
#### 移除指定索引位置array
    
    var array = ['a', 'b', 'c', 'd'];
    var pulled = _.pullAt(array, [1, 3]);
     
    console.log(array);
    // => ['a', 'c']
     
    console.log(pulled);
    // => ['b', 'd']
    


