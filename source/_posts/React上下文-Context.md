---
title: React 上下文Context
date: 2019-04-12 14:24:15
tags: [React,Javascript]
---

### React Context

相比props，context在React应用中不是很常见。props传递只能在父子关系组件逐级传递，如果传递层级比较深，就会非常痛苦，而Context可以跨级传递。当然redux很好解决这个问题

### React 上下文新api

在16.3.0中成为正式的api，使用方法也和之前的有差别。新版本的Context实现方式简洁清晰许多：
    
    import React, { Component, createContext } from 'react'
    
    // 通过createContext方法创建用于存放用户数据的Context类
    const UserContext = createContext()
    
    // 通过UserContext的属性Provider（组件）发布用户数据
    // 传递给Provider的value属性的值可以是任意数据类型，甚至是函数
    class App extends Component {
      state = {
        user: {
          name: 'abc',
        },
      }
      render() {
        const {
          user,
        } = this.state
        return (
          <UserContext.Provider value={user}>
            <UseUserInfoComponent />
          </UserContext.Provider>
        )
      }
    }
    
    // 通过UserContext的属性Consumer（组件）消费用户数据
    // Consumer的子组件必须是一个function，通过function的参数接收顶层传入的数据
    class UseUserInfoComponent extends Component {
      render() {
        return (
          <UserContext.Consumer>
            {
              context => (
                <div>
                  <p>{context.name}</p>
                </div>
              )
            }
          </UserContext.Consumer>
        )
      }
    }
    
### 在生命周期方法中引入Context

如果在某个组件上定义了 `contextTypes` ，下面这些生命周期方法将会接收到额外的参数——  context 对象。我们这里这样调整参数接口

* [constructor(props, context)](https://reactjs.org/docs/react-component.html#constructor)

* [componentWillReceiveProps(nextProps, nextContext)](https://reactjs.org/docs/react-component.html#componentwillreceiveprops)

* [shouldComponentUpdate(nextProps, nextState, nextContext)](https://reactjs.org/docs/react-component.html#shouldComponentUpdate)

* [componentWillUpdate(nextProps, nextState, nextContext)](https://reactjs.org/docs/react-component.html#componentWillUpdate)

* [componentDidUpdate(prevProps, prevState, prevContext)](https://reactjs.org/docs/react-component.html#componentDidUpdate)


### 在方法性组件中引入Context

    const PropTypes = require('prop-types');
    
    const Button = ({children}, context) =>
      <button style={{background: context.color}}>
        {children}
      </button>;
    
    Button.contextTypes = {color: PropTypes.string};


### 使用中需要注意的点

调用`creatContext`时可以传入默认值，但该值不是Provider的默认值，而是作为Consumer的默认值，仅当没有Provider的时候才生效

任何订阅者(Consumer)都可以直接修改context，这会导致后续的订阅者获取到修改后的context值，但这显然是不可取的。如果需要修改，应该统一由发布者(Provider)修改。

参考：

[滴滴AMC前端团队](https://zhuanlan.zhihu.com/p/42505277)

[随风溜达的向日葵](https://my.oschina.net/chkui/blog/876320)

