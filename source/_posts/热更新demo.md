---
title: 热更新demo
date: 2018-01-30 10:20:33
tags: [react-native, javascript]
---
## code-push 热更新 js css html 和图片

[go demo](https://github.com/WillCoco/codePushDemo.git)

### 1.安装code-push-cli

    $ npm install -g code-push-cli

### 2.注册账号

    $ code-push register

    这时候会自动启动浏览器打开网页并提供一个codePush AccessKey，然后命令行里出现需要输入access key 进行登录

### 3.添加一个CodePush应用

    code-push app add <appName> <platform> react-native

    获得 deployment-key（Production是对应生产环境的，Staging是对应开发环境的）：

![Demo](https://raw.githubusercontent.com/WillCoco/codePushDemo/master/pic/deployment_key.png)

### 4.react-native应用接入code-push

    $ npm install react-native-code-push --save
    $ react-native link react-native-code-push

    然后在项目中：

    import codePush from 'react-native-code-push';

    componentDidMount() {
      codePush.checkForUpdate(deploymentKey).then((update)=>{
          if(!update){
              Alert.alert("提示","已是最新版本--",[
                  {text:"Ok", onPress:()=>{
                      console.log("点了OK");
                  }}
              ]);
          }
          else{
              codePush.sync({
                  deploymentKey,
                  updateDialog: {
                      optionalIgnoreButtonLabel: '稍后',
                      optionalInstallButtonLabel: '后台更新',
                      optionalUpdateMessage: '有新版本了，是否更新？',
                      title: '更新提示'
                  },
                  installMode: codePush.InstallMode.IMMEDIATE
              });
          }
      })
    }

    其中deploymentKey就是之前获得的

### 修改代码

    巴拉巴拉

### 发布更新

    code-push release-react <appName> <platform>

    或者更复杂的：

    code-push release-react MyApp-iOS ios  --t 1.0.0 --dev false --d Production --des "1.优化操作流程" --m true

    其中参数–t为二进制(.ipa与apk)安装包的的版本；–dev为是否启用开发者模式(默认为false)；–d是要发布更新的环境分Production与Staging(默认为Staging)；–des为更新说明；–m 是强制更新。

更多信息请看[官方文档](https://github.com/Microsoft/react-native-code-push)
