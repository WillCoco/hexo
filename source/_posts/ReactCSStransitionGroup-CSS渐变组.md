---
title: ReactCSStransitionGroup-CSS渐变组
date: 2019-04-18 14:43:36
tags:
---

### ReactCSStransitionGroup

   ReactCSStransitionGroup是在插件类[ReactTransitionGroup](https://github.com/reactjs/react-transition-group)这个底层API基础上进一步封装的高级API，来简单的实现基本的CSS动画和过渡
    

### 简单用法

    render() {
      const items = this.state.items.map((item, i) => (
        <div key={item} onClick={() => this.handleRemove(i)}>
          <ReactCSSTransitionGroup transitionName="example">
            {item}
          </ReactCSSTransitionGroup>
        </div>
      ));
    
      return (
        <div>
          <button onClick={this.handleAdd}>Add Item</button>
          {items}
        </div>
      );
    }

ReactCSSTransitionGroup包装为包裹的元素在挂载和卸载生命周期阶段添加css类，然后手动添加约定css类名的具体样式来时现实一些简单出入场动画：

      .example-enter {
        opacity: 0.01;
      }
    
      .example-enter.example-enter-active {
        opacity: 1;
        transition: opacity 500ms ease-in;
      }
    
      .example-leave {
        opacity: 1;
      }
    
      .example-leave.example-leave-active {
        opacity: 0.01;
        transition: opacity 300ms ease-in;
      }
      
添加类名的时机可做延时：

    function ImageCarousel(props) {
      return (
        <div>
          <ReactCSSTransitionGroup
            transitionName="carousel"
            transitionEnterTimeout={300}
            transitionLeaveTimeout={300}>
            <img src={props.imageSrc} key={props.imageSrc} />
          </ReactCSSTransitionGroup>
        </div>
      );
    }
    
类名约定可定制：
    
      ...
      <ReactCSSTransitionGroup
        transitionName={ {
          enter: 'enter',
          enterActive: 'enterActive',
          leave: 'leave',
          leaveActive: 'leaveActive',
          appear: 'appear',
          appearActive: 'appearActive'
        } }>
        {item}
      </ReactCSSTransitionGroup>
    
      <ReactCSSTransitionGroup
        transitionName={ {
          enter: 'enter',
          leave: 'leave',
          appear: 'appear'
        } }>
        {item2}
      </ReactCSSTransitionGroup>
      ...

与`react-motion`包装不一样，`ReactCSSTransitionGroup`包装有实体元素，默认是`span`,可具体指定：
    
    <ReactTransitionGroup component=“ul” className="example" > 
	    ... 
    </ReactTransitionGroup>

> 注意:
 
> 你必须为ReactCSSTransitionGroup的所有子级提供  key 属性，即使只渲染一个项目。这就是React将决定哪一个子级进入，离开，或者停留

### 特点

是在react中写进出场动画不错的方式，性能也很好，就进出场动画而言比React Motion更方便。但是灵活性没有React Motion好，React Motion能做出更多的效果。

另外配合 css3动画，帧动画基本能很好的完成业务中动画需求。

查看[reactjs/react-transition-group](https://github.com/reactjs/react-transition-group)更多详细细节

其他React动画库：[React-Motion](https://willcoco.github.io/2019/04/17/React-Motion-dragable-balls/)



参考：

[React 动画实践](http://www.alloyteam.com/2016/01/react-animation-practice/#prettyPhoto)

[React Animation Add-ons](https://reactjs.org/docs/animation.html#high-level-api-reactcsstransitiongroup)
