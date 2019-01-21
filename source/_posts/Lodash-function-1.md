---
title: Lodash-function
tags:
  - Lodash
  - Javascript
categories: technology
comments: true
date: 2019-01-20 10:20:33
---


## Function

### _.after(n, func)
#### 方法内部维护n变量，在调用方法n次时及n次后执行func

    let f = _.after(2, alert);
    
    f(); // nothing
    f(); // alert
    f(); // alert
    
    
### _.before(n, func)
#### 类似after，前n次调用方法执行func
    
     let f = _.after(2, alert);
    
     f(); // alert
     f(); // alert
     f(); // nothing
     
### _.ary(func, [n=func.length])
#### 限制func参数个数为n个

    parseInt(11, 2); // 3
    
    let f = _.ary(parseInt, 1)'
    f(11); // 11
    
### _.unary(func)
#### 类似ary，限制func参数个数为1个

    _.map(['11', '10', '101'], _.unary(parseInt)); // 参数
    // => [11, 10, 101]
    
### _.curry(func, [arity=func.length])
#### 柯里化

    var abc = function(a, b, c) {
      return [a, b, c];
    };
     
    var curried = _.curry(abc);
     
    curried(1)(2)(3);
    // => [1, 2, 3]
     
    curried(1, 2)(3);
    // => [1, 2, 3]
     
    curried(1, 2, 3);
    // => [1, 2, 3]
     
    // 占位符的存在允许后边的参数先准备好
    curried(1)(_, 3)(2);
    // => [1, 2, 3]
    
### _.curryRight(func, [arity=func.length])
#### 类似curry，参数从右至左


### _.debounce(func, [wait=0], [options={}])
#### 函数防抖，延迟执行，wait内没有再次运行方法时调用一次
#### 应用：搜索框停止输入后发送请求
    options参数：
        maxWait：number // 在maxWait时间内至少执行一次
        leading： bool // 是否在开始时执行一次
        trailing： bool // 是否在时间边界结束时执行一次
        
### _.throttle(func, [wait=0], [options={}])
#### 函数节流，wait时间内调用无效，wait时间到了可以调用一次，进入新的wait时间
    options参数类似debounce
        
### _.defer(func, [args])
#### 执行栈清空时才执行，下一帧执行
    _.defer(function(text) {
      console.log(text);
    }, 'deferred');
    // => Logs 'deferred' after one millisecond.
    
### _.delay(func, wait, [args])
#### 延迟执行
    _.delay(function(text) {
      console.log(text);
    }, 1000, 'later');
    // => Logs 'later' after one second.
    
    
### _.flip(func)
#### 参数反转
    var flipped = _.flip(function() {
      return _.toArray(arguments);
    });
     
    flipped('a', 'b', 'c', 'd');
    // => ['d', 'c', 'b', 'a']
    
### _.memoize(func, [resolver])
#### 缓存函数。纯函数输入相同结果一定相同，所以如果对同样参数的同个纯函数执行结果有缓存，下次执行就不需要再次计算了

    function sum(n, m) {
        console.log(n, m);
        return n + m;
    }
    
    // 缓存
    let convert = _.memoize(sum);
    
    convert(1, 2); // 1, 2
    convert(1, 2); // nothing
    
### _.negate(predicate)
#### 求反
    let f = _.negate(() => true)
    f(); // false
    
    function isEven(n) {
      return n % 2 == 0;
    }
     
    _.filter([1, 2, 3, 4, 5, 6], _.negate(isEven));
    // => [1, 3, 5]
    
### _.once(func)
#### 只执行一次
    var initialize = _.once(createApplication);
    initialize();
    initialize();
    // => `createApplication` is invoked once
    
### _.overArgs(func, [transforms=[_.identity]])
#### 参数转换执行func前对参数转换
    function doubled(n) {a
      return n * 2;
    }
     
    function square(n) {
      return n * n;
    }
     
    var func = _.overArgs(function(x, y) {
      return [x, y];
    }, [square, doubled]);
     
    func(9, 3);
    // => [81, 6]
     
    func(10, 5);
    // => [100, 10]
    
### _.wrap(fn, [wrapper=identity])
#### 函数fn包裹，用第二个参数wrapper，包裹第一个参数函数
    var p = _.wrap(_.escape, function(func, text) {
      return '<p>' + func(text) + '</p>';
    });
     
    p('fred, barney, & pebbles');
    // => '<p>fred, barney, &amp; pebbles</p>'
    
### _.partial(func, [partials])
#### 缓存部分参数，后面的参数先准备好时可以用占位符
    function greet(greeting, name) {
      return greeting + ' ' + name;
    }
     
    var sayHelloTo = _.partial(greet, 'hello');
    sayHelloTo('fred');
    // => 'hello fred'
     
    // Partially applied with placeholders.
    var greetFred = _.partial(greet, _, 'fred');
    greetFred('hi');
    // => 'hi fred'
    
### _.rearg(func, indexes)
#### 重新分配参数顺序
    var rearged = _.rearg(function(a, b, c) {
      return [a, b, c];
    }, [2, 0, 1]);
     
    rearged('b', 'c', 'a')
    // => ['a', 'b', 'c']
    
### _.rest(func, [start=func.length-1])
#### 重置方法，参数位置不够的数组形式作为最后一个参数，也可指定位置开始
    var say = _.rest(function(what, names) {
        console.log(what)
        console.log(names)
    }
    
    say(a, b, c, d);
    // a
    // [b, c, d]
    
### _.spread(func, [start=0])
#### 类似apply, 参数变为数组
    var say = _.spread(function(who, what) {
      return who + ' says ' + what;
    });
     
    say(['fred', 'hello']);
    // => 'fred says hello'
     
    var numbers = Promise.all([
      Promise.resolve(40),
      Promise.resolve(36)
    ]);
     
    numbers.then(_.spread(function(x, y) {
      return x + y;
    }));
    // => a Promise of 76