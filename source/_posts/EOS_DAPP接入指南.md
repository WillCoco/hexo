---
title: EOS DAPP 接入指南
tags: [blockchain, javascript]
date: 2019-03-01 10:10:10
---
　　　　

<script>
    const title = document.getElementsByClassName('post-title')[0];
    const isHome = document.getElementsByClassName('post-title-link').length > 0;
    if (!isHome && title) {
    
       console.log(document.styleSheets[0].rules[0])
       try {
           document.styleSheets[0].insertRule(
           `.mystery-title::after{
               content: '/' attr(data-content);
               position:relative;
               left:-15px;
               color:#ff0000
           }`,
           0)//chrome,firefox等非IE浏览器使用
       } catch(err) {
           console.log(err)
       }
       try {
           document.styleSheets[0].addRule('.mystery-title::before{position:relative;left:-10px;color:green}',0)//IE系列浏览器使用
       } catch(err) {
           console.log(err)
       }
       if (!title.className.match('mystery-title')) {
           title.className += ' mystery-title'; 
           let direction = ['北','北','北','↑','↓','←','→'];
           let timerCount = 0;
           title.setAttribute('data-content', ' '+direction[parseInt(Math.random() * direction.length)]);
           timerCount++;
           setInterval(() => {
                title.setAttribute('data-content', ' '+direction[parseInt(Math.random() * direction.length)]);
                timerCount++;
           }, Math.random()*5000+500)
       }
    }
</script>



#### [1. 前言](#前沿)

#### [2. scatter EOS DAPP](#scatter dapp中主要的钱包交互)

#### [3. 注入原理](#注入原理)

#### [4. 常见操作覆写](#常见操作覆写)


### 前沿

EOS主网上线至今，有越来越多的EOS Dapp上线。其中有很多dapp都是基于[scatter](https://https://get-scatter.com)钱包的接口，只需实现游戏逻辑，私钥管理，交易收发等等都只要交给scatter就可以。
当我们有自己的EOS钱包，就可以接入这些DAPP达到一些推广钱包等目的。

### scatter dapp中主要的钱包交互

引入包，定义网络信息:
```$xslt
    import EOS from "eosjs";
    import ScatterJS from "scatterjs-core";
    import ScatterEOS from "scatterjs-plugin-eosjs";
    
    const network = {
        blockchain: "eos",
        host: "mainnet.eoscanada.com",
        port: 443,
        protocol: "https",
        chainId: "aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906"
    };
```

监听scatter钱包加载:   
```
   document.addEventListener('scatterLoaded', () => {
       ScatterJS.scatter.connect("p3d")
           .then(connected => {
               if (!connected) return false;
               
               EOSClient = ScatterJS.scatter.eos(network, EOS);
           })
   });
```

获取身份信息:
```
    function getIdentity() {
        return () => {
            ScatterJS.scatter && ScatterJS.scatter.getIdentity({ accounts: [network] })
                .then(() => {
                  // 获取身份成功
                  const identity = ScatterJS.account('eos');
                  console.log(identity, 'this.scatter.identity.accounts');
                })
                .catch((err) => {
                  console.log(err, '获取身份失败');
                });
        }
    }
```

发送交易，或者其他合约操作的三个接口:
```$xslt
    // 普通交易，参数是对象风格的或者类数组风格的两种方式
    EOSClient.transfer()
```
```
    // 合约操作1
    EOSClient.transaction(
      {
       // ...headers,
       actions: [
           {  
               account: contract, // 合约名
               name: actionName, // 操作名
               authorization: [{
                 actor: identity.name,
                 permission: identity.authority
               }],
               data: {
                 from: identity.name,
                 to: to,
                 quantity,
                 memo
               }
           }  
       ]
      }
    )
      .then(res => {console.log(res, 'fasong token')})
```
```$xslt
    // 合约操作2，chrome扩展scatter不指定authorization会报错，可能是没有正确识别active/owner权限
    EOSClient.contract('ethsidechain')
        .then(r => {
            r.transfer(
              identity.name,
              to,
              '1.0000 EETH',
              memo,
              { authorization: [`${identity.name}@${identity.authority}`]}
            ).then(r => {
              console.log(r, 'transfer');
            })
        });
```

### 注入原理

覆写window下的scatter,如此当dapp调用的scatter钱包接口已经替换为我们钱包的接口
```$xslt
    window.scatter = {
        ...
        getIdentity: function() {
            ...
        },
        eos: (network, EOS) => {
            transfer() {
            },
            contract() {
            },
            
        }
    }
```

发送scatter加载事件来自动登录
```$xslt
    var event = document.createEvent('HTMLEvents');
    event.initEvent("scatterLoaded", true, true);
    event.eventType = 'scatterLoaded';
    document.dispatchEvent(event);
```

*调试多个dapp网站，发现有些将scatter挂载在window.Scatter.scatter下，有些则将scatter和EOSClient定义在局部变量中，并将window.scatter设为null，实际这些情况都不影响以上方式的注入覆写。乍看之下很困惑，简单分析下scatterjs得到了答案：

1.ScatterJS不是标准模块化的，污染了全局变量scatter，也就是说import ScatterJS库之后，就有了window.scatter。且和Scatter.scatter是同一个引用：
    
    ScatterJS.scatter === window.scatter; // true
    
2.加载dapp网站代码，代码中注册了对`scatterLoaded`事件的监听:

```$xslt
    document.addEventListener('scatterLoaded', () => {
     ScatterJS.scatter.connect("p3d")
         .then(connected => {
             if (!connected) return false;
             
             EOSClient = ScatterJS.scatter.eos(network, EOS);
             
             // 后边的EOS操作通过调用EOSClient.transfer ...
         })
   });
```

    
3.模拟`scatterLoaded`事件

    var event = document.createEvent('HTMLEvents');
    event.initEvent("scatterLoaded", true, true);
    event.eventType = 'scatterLoaded';
    document.dispatchEvent(event)
    
4.dapp中收到scatterLoaded事件后，EOSClient顺利成章的拿到了覆写的window.scatter方法




