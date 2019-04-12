---
title: React16.8新特性
date: 2019-04-12 10:03:39
tags: [javascript,react]
---

今天查看React源码时候注意到一些很陌生的东西，比如memo、lazy、useState等use系列，这些东西以前没见过，难不成react又有新特性，新api？google一下果然如此

### 一、memo

memo的功能感觉和shouldComponentUpdate非常类似，但不像shouldComponentUpdate只能作为component类中的生命周期方法，memo更加的贴合函数式编程理念，
可以用在function component中。

react源码：
  
    function memo(type, compare) {
      {
        if (!isValidElementType(type)) {
          warningWithoutStack$1(false, 'memo: The first argument must be a component. Instead ' + 'received: %s', type === null ? 'null' : typeof type);
        }
      }
      return {
        $$typeof: REACT_MEMO_TYPE,
        type: type,
        compare: compare === undefined ? null : compare
      };
    }
    
在fiber相关文件ReactFiberBeginWork.js中可以看到：

    if (updateExpirationTime < renderExpirationTime) {
        // This will be the props with resolved defaultProps,
        // unlike current.memoizedProps which will be the unresolved ones.
        const prevProps = currentChild.memoizedProps;
        // Default to shallow comparison
        let compare = Component.compare;
        compare = compare !== null ? compare : shallowEqual;
        if (compare(prevProps, nextProps) && current.ref === workInProgress.ref) {
          return bailoutOnAlreadyFinishedWork(
            current,
            workInProgress,
            renderExpirationTime,
          );
        }
      }

根据传入的compare函数比较prevProps和nextProps，最终决定生成对象，并影响渲染效果。

例子：
    
    const SubComponent = props => 
      <p>
        i am {props.name}. hi~
      </p>

    const Memo = React.memo(SubComponent, (prevProps, nextProps) => 
      prevProps.name === nextProps.name
    );

    <div className="App">
      <Memo name={name} />
    </div>

memo接收两个参数，一个是组件，一个是函数。这个函数就是定义了memo需不需要render的钩子。
比较前一次的props跟当前props，返回true表示不需要render。
也就是传给Memo的name不变时，不会触发SubComponent的render函数。
当前页面上的SubComponent还是之前的，并没有重新渲染。这也是为啥叫memo的原因吧。


### 二、lazy & suspence

这部分是做代码拆分、按需加载的。之前可以用一些三方库来完成按需加载，比如[react-loadable](https://github.com/jamiebuilds/react-loadable)、[react-router-v4:code-splitting](https://reacttraining.com/react-router/web/guides/code-splitting)等，React.lazy的提出是一种更优雅的条件渲染解决方案。之所以说他更优雅，是因为他将条件渲染的优化提升到了框架层。用法如下：

    import React, {lazy} from 'react';
    const OtherComponent = lazy(() => import('./OtherComponent'));
    
    function MyComponent() {
      return (
        <div>
          <OtherComponent />
        </div>
      );
    }
    
suspense用来指定还在loading时的降级UI：

    const OtherComponent = React.lazy(() => import('./OtherComponent'));
    
    function MyComponent() {
      return (
        <div>
          <Suspense fallback={<div>Loading...</div>}>
            <OtherComponent />
          </Suspense>
        </div>
      );
    }
    
### 三、hooks

#### hooks是什么？

> Hooks are a new addition in React 16.8. They let you use state and other React features without writing a class.

官方解释的很清晰，hooks就是能让你在function component中也能用React.Component类中特性（state，部分生命周期等）的新特性。

hooks常用api有：useState、useEffect、useContext、useReducer、useRef、useMemo等。

#### useState可以定义、使用和修改state：

	import { useState } from "react";
	
	function App() {
	  const [open, setOpen] = useState(false);
	  return (
	    <>
	      <Button type="primary" onClick={() => setOpen(true)}>
	        Open Modal
	      </Button>
	      <Modal
	        visible={open}
	        onOk={() => setOpen(false)}
	        onCancel={() => setOpen(false)}
	      />
	    </>
	  );
	}

#### useEffect 

类似 React.Component中的 DidMount和DidUpdate。也就是说useEffect第一次渲染和每次更新都会触发useEffect，需要注意开销和优化。在return中清理一些需要清除的effects。第二个列表参数指定useEffect只响应这些props.source中props改变带来的更新。

	import { useState, useEffect } from "react";
		
	useEffect(() => {
	  const subscription = props.source.subscribe();
	  return () => {
	    // Clean up the subscription
	    subscription.unsubscribe();
	  };
	}, [props.source]);


#### [内置 Hooks和其他Hooks](https://reactjs.org/docs/hooks-reference.html)

#### 状态与 UI 的界限会越来越清晰

React Hooks 本质就是 renderProps 或者 HOC 的另一种写法。通过hooks和抽象大量 Custom Hooks，可以说我们又有了一种更加函数式的，少嵌套的，无状态组件的组织方式，

参考：
1.	[React docs](https://reactjs.org/docs/hooks-reference.html)
2.	[精读《React Hooks》](https://juejin.im/post/5be8d3def265da611a476231)
3.	[React新特性实例详解](http://react-china.org/t/react-memo-lazy-suspense-hooks/28789)


