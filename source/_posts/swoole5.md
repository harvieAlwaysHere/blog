---
title: Swoole(五)配置WebSocket服务器
tags:
  - php项目
  - swoole
date: 2018-02-16 20:21:49
categories: ItEassy
---
### 接来下我们来学习配置一个WebSocket服务器
![](/images/swoole.jpg)
---
### 1.首先介绍下什么是websocket
初次接触 WebSocket 的人，都会问同样的问题：我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？
答案很简单，因为 HTTP 协议有一个缺陷：**通信只能由客户端发起**。
这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。
一般有两种方法
*  **AJAX轮询**：让浏览器隔个几秒就发送一次请求，询问服务器是否有新信息。
*  **Long Poll**：原理跟AJAX轮询差不多，都是采用轮询的方式，不过采取的是**阻塞模型**，也就是说，客户端发起连接后，如果没消息，就一直不返回Response给客户端。直到有消息才返回，返回完之后，客户端再次建立连接，周而复始。 

**WebSocket协议**在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。
它的最大特点就是，**服务器可以主动向客户端推送信息**，客户端也可以**主动向服务器发送信息**，是真正的**双向平等对话**，属于**服务器推送技术**的一种。

---
### 2.Swoole实现Websocket服务端
先上代码然后解释
{% codeblock %}
<?php

$ws = new swoole_websocket_server("0.0.0.0", 9504);

$ws->on('open', function ($ws, $request) {
    var_dump($request);
    $ws->push($request->fd,"welcome to harvie's home !\n")
});

$ws->on('message', function ($ws, $frame) {
    echo "receive from {$frame->fd}:{$frame->data},opcode:{$frame->opcode},fin:{$frame->finish}\n";
    $ws->push($frame->fd, "get it message !");
});

$ws->on('close', function ($ws, $fd) {
    echo "client {$fd} closed\n";
});

$ws->start();
{% endcodeblock %}
#### 3.代码解释
1.**构建websocket服务器对象**：swoole_websocket_server($ip,$port)
2.**设置回调函数事件**:
2.1 **open事件**：建立连接事件， function (swoole_websocket_server $server, $request)，$server是服务器信息，$request是客户端信息
2.2 **message事件**：接收消息事件，function (swoole_websocket_server $server, $request)，$server是服务器信息，$frame是客户端信息
2.3 **close**：关闭连接事件，function (swoole_websocket_server $server, $fd)，$server是服务器信息，$frame是客户端信息
2.4 使用**swoole_websocket_server->push(int $fd, string $data, int $opcode = 1, bool $finish = true);**向websocket客户端连接推送数据
* **$fd**为客户端连接的ID
* **$data**为发送的数据内容
* **$opcode**指定发送数据内容的格式，默认为文本。发送二进制内容$opcode参数需要设置为WEBSOCKET_OPCODE_BINARY
* **$finish**发送成功返回true，发送失败返回false
3.启动服务器

---
#### 4.测试运行
将脚本上传到服务器并运行，打开浏览器，输入ip地址和端口号可以看到
![](/images/swoole5.png)
并不能成功访问，必须**实现WebSocket协议才能和WebSocket服务器通信**，我们创建一个前台websocket页面，代码如下
{% codeblock %}
<!DOCTYPE html>
<html>
<head lang="en">
	<meta charset="utf-8">
	<title></title>
</head>
<body>
	<script type="text/javascript">
		var wsServer = 'ws://39.108.210.229:9504';
		var websocket = new WebSocket(wsServer);
		websocket.onopen = function (evt) {
		    console.log("连接成功");
		};

		websocket.onclose = function (evt) {
		    console.log("连接关闭");
		};

		websocket.onmessage = function (evt) {
		    console.log('从服务器接受到的数据为：' + evt.data);
		};

		websocket.onerror = function (evt, e) {
		    console.log('错误发生信息: ' + evt.data);
		};
	</script>
</body>
</html>
{% endcodeblock %}
然后放在我们Windows客户端下的Apache服务器下执行，可以打开浏览器看到
![](/images/swoole51.png)
此时我们可以看到服务器中打印了相关的请求信息
![](/images/swoole52.png)

