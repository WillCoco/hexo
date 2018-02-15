---
title: electron打包后表单控件复制粘贴失效解决办法
categories: technology
tags: electron
comments: true
data: 2017/11/10
---

## mac系统下快捷键问题

在Mac系统下，打完包的electron App中，发现Redo、Undo、复制粘贴等快捷键无效。解决此问题需要此在main.js中注册Menu，直接上代码：

{% codeblock %}
   //step1: 引入electron的Menu模块
   var Menu = require('electron').Menu;

   //step2: 快捷键映射关系
   let template = [{
       label: "Application",
       submenu: [
           { label: "About Application", selector: "orderFrontStandardAboutPanel:" },
           { type: "separator" },
           { label: "Quit", accelerator: "Command+Q", click: function() { app.quit(); }}
       ]}, {
       label: "Edit",
       submenu: [
           { label: "Undo", accelerator: "CmdOrCtrl+Z", selector: "undo:" },
           { label: "Redo", accelerator: "Shift+CmdOrCtrl+Z", selector: "redo:" },
           { type: "separator" },
           { label: "Cut", accelerator: "CmdOrCtrl+X", selector: "cut:" },
           { label: "Copy", accelerator: "CmdOrCtrl+C", selector: "copy:" },
           { label: "Paste", accelerator: "CmdOrCtrl+V", selector: "paste:" },
           { label: "Select All", accelerator: "CmdOrCtrl+A", selector: "selectAll:" }
       ]}
   ];

   //step3: 在ready后注册
   app.on('ready', () => {
     ...
     //注册菜单
     Menu.setApplicationMenu(Menu.buildFromTemplate(template));
   })

{% endcodeblock %}

到此问题就解决了
