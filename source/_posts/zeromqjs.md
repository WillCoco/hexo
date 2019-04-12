---
title: zeromq.js介绍和事例
tags: [ZeroMQ, Javascript]
date: 2019-01-31 16:46:04
---

### [1. zeromq介绍](#介绍)

### [2. 几种消息模式](#几种消息模式)
 
### [3. req/rep listener转换成promise](#req/rep listener转换成promise)

### [4. demo](#demo)
 
### 介绍
zeromq是一个为可伸缩的分布式或并发应用程序设计的高性能异步消息库。它提供一个消息队列, 但是与面向消息的中间件不同，ZeroMQ的运行不需要专门的消息代理（message broker）。该库设计成常见的套接字风格的API。支持多种语言，zeromq.js是zeromq的node绑定库，这里的例子是基于[zeromq.js](https://github.com/zeromq/zeromq.js)
                                                                                                                                                            

### 几种消息模式

#### pub/sub模式

  <img src="/images/zeromq/zeromq_3.png" width="400" />

发布订阅模式 发布端单向分发数据，且不关心是否把全部信息发送给订阅端。如果发布端开始发布信息时，订阅端尚未连接上来，则这些信息会被直接丢弃。订阅端只负责接收，而不能反馈，且在订阅端消费速度慢于发布端的情况下，会在订阅端堆积数据。该模型主要用于有时效性的数据分发。实时价格、天气预报、微博明星粉丝可以应用这种经典模型。订阅端未连接时发布者发布的信息会丢失，如果需要这些过去的信息，可以通过与请求回应模型组合来解决。

*  订阅者可以连接多个发布者，轮流接收消息；
*  如果发布者没有订阅者与之相连，那它发送的消息将直接被丢弃；
*  如果你使用TCP协议，那当订阅者处理速度过慢时，消息会在发布者处堆积。


#### push/pull模式

  <img src="/images/zeromq/zeromq_0.png" width="400" />

Server端作为Push端，而Client端作为Pull端，如果有多个Client端同时连接到Server端，则Server端会在内部做一个负载均衡，采用平均分配的算法，将所有消息均衡发布到Client端上。如果有多个Server端同时连接到Client端，这里Push与Pull之间的对应关系是多个Push角色对应一个Pull角色，在ZeroMQ中，给这种结构取的名叫做公平队列，这里也就是说将Pull角色理解为一个队列，各个Push角色不断的向这个队列中发送数据。与发布订阅模型相比，推拉模型在没有消费者的情况下，发布的消息不会被消耗掉；在消费者能力不够的情况下，能够提供多消费者并行消费解决方案。该模型主要用于多任务并行处理。

* worker链接之前的消息不会丢弃
* 所有worker接收的信息的集合就是push端推送的消息

#### rep/req模式

  <img src="/images/zeromq/zeromq_1.png" width="200" />

问答模式，由请求端发起请求，然后等待回应端应答。一个请求必须对应一个回应，从请求端的角度来看是发-收配对，从回应端的角度是收-发对。请求端可以是1~N个。该模型主要用于远程调用及任务分配等。服务端在前一个处理完之前接收不到后面的请求，所以所有请求和返回都是对应顺序的。如果要非阻塞方式，请用[router/dealer模式](#router/dealer模式)

*  一问一答都是按顺序的，阻塞的，请求1-回应1 请求2-回应2；

#### router/dealer模式

DEALER和ROUTER是插座，可以轻松扩展REQ / REP对。在直接通信中，REQ和REP以阻塞方式进行通话，而该模式是非阻塞的。客户端需要identity来告诉服务端客户端身份，请求处理后返回给谁。请求中可以指定请求编号来区分回复对应的是哪个请求。

  <img src="/images/zeromq/zeromq_2.png" width="400" />

### req/rep listener转换成promise

事例演示了如何把监听(listener)的异步方式封装成promise
[dealer-listener2Promise.js](https://github.com/WillCoco/zeromqjs_demo/blob/master/router-dealer/dealer-listener2Promise.js)

### demo
[zeromqjs_demo](https://github.com/WillCoco/zeromqjs_demo)

### GUIDE
[zguide](http://zguide.zeromq.org/)


