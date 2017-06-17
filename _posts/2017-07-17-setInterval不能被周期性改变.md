---
layout: post
title: setInterval不能被周期性改变
date: 2017-07-17
categories: blog
tags: [js]
description: setInterval不能被周期性改变,带参数

---

在JS中无论是setTimeout还是setInterval，在使用函数名作为调用句柄时都不能带参数，而在许多场合必须要带参数，接下来为大家介绍具体的解决方法
在JS中无论是setTimeout还是setInterval，在使用函数名作为调用句柄时都不能带参数，而在许多场合必须要带参数， 
这就需要想方法解决。 
一、采用字符串形式：——（缺陷）参数不能被周期性改变 
setInterval("foo(id)",1000); 
二、匿名函数包装 (推荐) 
复制代码 代码如下:

window.setInterval(function() 
{ 
foo (id); 
}, 1000); 

这样就可以周期性执行foo（id）这个函数，而且把变量id传递进去；
