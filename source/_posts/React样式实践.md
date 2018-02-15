---
title: React样式实践
tags: react
categories: technology
comments: true
date: 2017-11-22 11:26:13
---


  CSS可以最大限度的分离样式和内容，选择器也可以很方便的给某些元素添加样式。你根本找不到任何不用CSS的理由。但是在React这里就是另外一回事了，虽然React不是不用CSS。但是，它在给元素添加样式的时候方式不同。

  React的核心哲学之一就是让可视化的组件自包含，并且可复用。这就是为什么HTML元素和Javascript放在一起组成了Component（组件）。React的自包含组件需要在定义的时候就定义好样式，这样才能实现自包含。

### react中样式的几种写法
1.css写法

  xxx.css:

    .container {
      margin-left: 10px;
    }

  xxx.js:

    //引入css
    import style from 'xxx.css'

    //使用className
    <div className={style.container}>
      ...
    </div>

2.行内样式


    //jsx中是驼峰命名
    <div style={{ marginLeft: 10 }}>
      ...
    </div>


3.对象样式 和 行内样式没什么本质区别


    const styles = {
      marginLeft: 10,
      ...
    }
    <div style={{ marginLeft: 10 }}>
      ...
    </div>


### 以上看来各种用法都没有问题，但是看下面的例子：

一个封装好的组件：

    <ListItem
      key={1}
      innerDivStyle={{ marginLeft: basicMargin }}
      primaryText= {<PrimaryText data-text="总览" />}
      leftIcon={<ContentInbox viewBox={Metrics.iconNormal} style={{}} />}
      hoverColor={activeColor}
      style={{}}
    />


在ListItem下的style的属性 和 leftIcon中的style属性都可以替换为 className，但是innerDivStyle，它是组件中的内框，又只能用对象样式的方法来书写。

另外，注意到`hoverColor` & `marginLeft`的属性值，我希望他们是统一在项目的Theme资源下定义的，而不是在每个文件里满天飞。如果是对象样式，它可以这样定义js or json文件方便读取：

Theme/Colors.js:

    let colors = {
      warn: '#ff000',
      hoverColor: '#ccc'
    }

    export default colors;

Theme/Metrics.js:

    let metrics = {
      basicMargin: 10
    }

    export default metrics;


xxx.js中很方便的使用:

    import Color from '../Theme/Color'
    import Metrics from '../Theme/Metrics'
    ...

    <ListItem
      key={1}
      innerDivStyle={{ marginLeft: Metrics.basicMargin }}
      primaryText= {<PrimaryText data-text="总览" />}
      leftIcon={<ContentInbox viewBox={Metrics.iconNormal} style={{}} />}
      hoverColor={Color.activeColor}
      style={{}}
    />

我们再来试试css方式怎么用，sass使得写css像写js一样使用非常灵活，可定义变量等方式来减少重复代码,它在构建阶段被编程普通的css, 需要在webpack中配置loader,我们在这只看它的使用：

Theme/Colors.scss:

    $warn: red;
    $activeColor: #ccc;

    .warnBackground {
      background-color: red
    }

可以被其他scss文件引用, 然而在js中:

xxx.js

    import styles from 'Theme/xxx.scss'

    <ListItem
      key={1}
      innerDivStyle={{ ContentInbox: ?? }}
      primaryText= {<PrimaryText data-text="总览" />}
      leftIcon={<ContentInbox viewBox={Metrics.iconNormal} className={{}} />}
      hoverColor={??}
      className={styles.warnBackground}
    />

到这就懵逼了，js读取scss定义的属性值 或者 sass从其他js json配置 都不那么容易。 那么组件jsx的属性`hoverColor` & `ContentInbox`怎么传入Theme中的值？

到此总结 在自己造的轮子中， 明确对外开放的属性中，样式定制只通过改变className的， 可以使用css。相信在大部分项目中都会使用其他好用的轮子，包括上面例子中使用的ListItem来自 google的material-ui, 感觉和对象样式或者叫LineStyle更搭配。

各种技术和技术架构总是为产品服务，能满足需求节省开发时间的就是合理的。为了项目整体更整洁，更app，我要把css改成中改成使用对象样式，另外在写动画组件时，还是很愿意尝试css3的动画属性。
