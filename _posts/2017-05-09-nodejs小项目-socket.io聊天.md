---
layout: post
title: nodejs小项目-socket.io 聊天
date: 2017-05-09
categories: blog
tags: [node]
description: 一个20行代码的socket.io 聊天

---
转自 https://socket.io/get-started/chat/ 
网络框架
第一个目标是设置一个简单的HTML网页，提供表单和消息列表。为此，我们将使用Node.JS Web框架express。确保Node.js的安装。

首先我们来创建一个package.json描述我们项目的清单文件。我建议你将它放在一个专用的空目录中（我会打电话给我chat-example）。


{
  "name": "socket-chat-example",
  "version": "0.0.1",
  "description": "my first socket.io app",
  "dependencies": {}
}
    
现在，为了轻松填充dependencies我们需要的东西，我们将使用npm install --save：

npm install --save express@4.15.2
现在安装了express，我们可以创建一个index.js将设置我们的应用程序的文件。


var app = require('express')();
var http = require('http').Server(app);

app.get('/', function(req, res){
  res.send('<h1>Hello world</h1>');
});

http.listen(3000, function(){
  console.log('listening on *:3000');
});
    
这转换成以下内容：

Express初始化app为可以提供给HTTP服务器的函数处理程序（如第2行所示）。
我们定义一个路由处理程序/，当我们在我们的网站回家时被调用。
我们使http服务器在端口3000上侦听。
如果你跑步，node index.js你应该看到如下：



如果您将浏览器指向http://localhost:3000：



提供HTML
到目前为止，index.js我们正在调用res.send并传递一个HTML字符串。如果我们把整个应用程序的HTML放在那里，我们的代码会变得很混乱。相反，我们将创建一个index.html文件并提供服务。

让我们重构我们的路由处理程序来使用sendFile：


app.get('/', function(req, res){
res.sendFile(__dirname + '/index.html');
});
    
并填充index.html以下内容：


<!doctype html>
<html>
  <head>
    <title>Socket.IO chat</title>
    <style>
      * { margin: 0; padding: 0; box-sizing: border-box; }
      body { font: 13px Helvetica, Arial; }
      form { background: #000; padding: 3px; position: fixed; bottom: 0; width: 100%; }
      form input { border: 0; padding: 10px; width: 90%; margin-right: .5%; }
      form button { width: 9%; background: rgb(130, 224, 255); border: none; padding: 10px; }
      #messages { list-style-type: none; margin: 0; padding: 0; }
      #messages li { padding: 5px 10px; }
      #messages li:nth-child(odd) { background: #eee; }
    </style>
  </head>
  <body>
    <ul id="messages"></ul>
    <form action="">
      <input id="m" autocomplete="off" /><button>Send</button>
    </form>
  </body>
</html>
    
如果重新启动该过程（通过击中Control + C并node index再次运行）并刷新页面，应该如下所示：



集成Socket.IO
Socket.IO由两部分组成：

与Node.JS HTTP Server集成（或挂接）的服务器： socket.io
在浏览器端加载的客户端库： socket.io-client
在开发过程中socket.io，为我们自动为我们提供服务，所以现在我们只需要安装一个模块：

npm install --save socket.io
这将安装模块并添加依赖关系package.json。现在我们来编辑index.js来添加它：


var app = require('express')();
var http = require('http').Server(app);
var io = require('socket.io')(http);

app.get('/', function(req, res){
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', function(socket){
  console.log('a user connected');
});

http.listen(3000, function(){
  console.log('listening on *:3000');
});
    
请注意，我socket.io通过传递http（HTTP服务器）对象来初始化一个新的实例。然后我听connection事件的传入套接字，我登录到控制台。

现在在index.html我添加以下代码片段之前</body>：


<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.1/socket.io.js"></script>
<script>
  var socket = io();
</script>
    
这就是加载它所需要的socket.io-client，它暴露了io全局，然后连接。

请注意，当我打电话时，我没有指定任何URL io()，因为它默认尝试连接到提供该页面的主机。

如果您现在重新加载服务器和网站，您应该看到控制台打印“连接用户”。
尝试打开几个选项卡，您会看到几个消息：



每个插座还会触发一个特殊disconnect事件：


io.on('connection', function(socket){
  console.log('a user connected');
  socket.on('disconnect', function(){
    console.log('user disconnected');
  });
});
    
那么如果你刷新一个标签多次，你可以看到它的行动：



发射事件
Socket.IO背后的主要思想是，您可以发送和接收任何您想要的事件，并提供所需的任何数据。任何可以编码为JSON的对象都可以做，二进制数据也被支持。

让我们这样做，以便当用户键入消息时，服务器将其当作chat message事件。现在script的部分index.html现在应该如下：


<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/2.0.1/socket.io.js"></script>
<script src="https://code.jquery.com/jquery-1.11.1.js"></script>
<script>
  $(function () {
    var socket = io();
    $('form').submit(function(){
      socket.emit('chat message', $('#m').val());
      $('#m').val('');
      return false;
    });
  });
</script>
      
在index.js我们打印出的chat message事件中：


io.on('connection', function(socket){
  socket.on('chat message', function(msg){
    console.log('message: ' + msg);
  });
});
    

广播
下一个目标是让我们从服务器发送到其他用户的事件。

为了向大家发送一个事件，Socket.IO给了我们io.emit：

io.emit('some event', { for: 'everyone' });
如果你想发送消息给所有人，除了某个套接字，我们有broadcast标志：


io.on('connection', function(socket){
  socket.broadcast.emit('hi');
});
    
在这种情况下，为了简单起见，我们会将消息发送给所有人，包括发件人。


io.on('connection', function(socket){
  socket.on('chat message', function(msg){
    io.emit('chat message', msg);
  });
});
    
而在客户端，当我们捕获一个chat message事件时，我们将其包含在页面中。客户端JavaScript代码总共等于：


<script>
  $(function () {
    var socket = io();
    $('form').submit(function(){
      socket.emit('chat message', $('#m').val());
      $('#m').val('');
      return false;
    });
    socket.on('chat message', function(msg){
      $('#messages').append($('<li>').text(msg));
    });
  });
</script>
    
这完成了我们的聊天应用程序，大约20行代码！


