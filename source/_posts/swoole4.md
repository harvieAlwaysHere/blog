---
title: Swoole(四)配置Web服务器
tags:
  - php项目
  - swoole
date: 2018-02-16 19:56:47
categories: ItEassy
---
### 接来下我们来学习Swoole的基本函数，以此来配置一个Web服务器
![](/images/swoole.jpg)
---
### 1.基本函数
#### 1.1 swoole_http_server：swoole_http_server继承自swoole_server，是一个完整的http服务器实现，支持同步和异步2种模式。
函数原型：$http = new swoole_http_server($ip, $port);
* **$ip**：监听的ip
* **$port**：监听的端口

#### 1.2 swoole_http_server->on:注册事件回调函数。
swoole_http_server->on**不接受onConnect/onReceive回调**设置，swoole_http_server->on 额外**接受1种新的事件类型onRequest**
{% codeblock %}
$http_server->on('request', function($request,$response) {
     $response->end("<h1>hello swoole</h1>");
});
{% endcodeblock %}
在收到一个完整的Http请求后，会回调此函数。回调函数共有2个参数：
* **$request**：Http请求信息对象，包含了header/get/post/cookie等相关信息
* **$response**：Http响应对象，支持cookie/header/status等Http操作
* **$response->end()**：向客户端发送信息
Tips：在onRequest回调函数返回时底层会销毁$request和$response对象，如果未执行$response->end()操作，底层会自动执行一次$response->end("")

### 2.构建基本的Web服务器
1.**构建swoole_http_server对象**，设置监听对象为所有ip地址，端口为9503端口
2.**注册回调事件request**，打印请求信息对象和返回Hello Swoole+一个随机数(证明每次访问返回不同)，$response->end()方法表示输出一段HTML内容，并结束此请求。
3.**启动服务器**
{% codeblock %}
$http = new swoole_http_server("0.0.0.0", 9503);

$http->on('request', function ($request, $response) {
    var_dump($request);
    $response->header("Content-Type", "text/html; charset=utf-8");
    $response->end("<h1>Hello Swoole. #".rand(1000, 9999)."</h1>");
});

$http->start();
{% endcodeblock %}
我们先上传php脚本到服务器，之后执行
![](/images/swoole41.png)
然后我们使用浏览器访问服务器的ip地址，可以看到
![](/images/swoole42.png)
此时我们可以看到服务器会打印请求对象的信息
![](/images/swoole43.png)
我们再次刷新服务器可以看到随机数会变化
![](/images/swoole44.png)