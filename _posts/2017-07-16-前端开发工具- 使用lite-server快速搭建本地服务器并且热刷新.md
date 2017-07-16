---
layout: post
title:  前端开发工具-快速搭建本地服务器并且热刷新 .
date: 2016-02-29
categories: blog
tags: [swift]
description: 使用lite-server快速搭建本地服务器并且热刷新

---

使用Nodejs模块搭建本地服务器的方法很多，但是有的方法较为麻烦，且功能单一不易拓展。

lite-server的功能与之类似，均可以搭建本地服务器，但它的优势在于搭建迅速，只需要安装npm包即可；可自动刷新，使用BrowserSync监测文件变化；可配置多种选项，比如默认端口及默认文件夹等。

npm地址：https://www.npmjs.com/package/lite-server

迅速搭建： 
npm init 
npm install --save-dev lite-server

修改package.json文件，加入如下语句：

"scripts": {    
   "dev": "lite-server"
 },

npm run dev

这样就启动了一个本地服务器，修改URL来访问目标文件。

默认端口是3000，如果想使用其他端口需要修改配置： 
新建bs-config.js文件，并写入如下内容（以8000为例）：

{
  "port": 8000
}
