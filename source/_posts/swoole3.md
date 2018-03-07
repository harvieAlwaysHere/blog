---
title: Swoole()配置UDP服务器
tags:
  - php项目
  - swoole
date: 2018-02-16 17:23:01
categories: ItEassy
---
### 接来下我们来学习Swoole的基本函数，以此来配置一个UDP服务器
![](/images/swoole.jpg)
---
### 1.基本函数
#### 1.1 swoole_server->sendto：向任意的客户端IP:PORT发送UDP数据包。
函数原型：bool swoole_server->sendto(string $ip, int $port, string $data, int $server_socket = -1);
* **$ip**:为IPv4字符串，如192.168.1.102。如果IP不合法会返回错误
* **$port**:为 1-65535的网络端口号，如果端口错误发送会失败
* **$data**:要发送的数据内容，可以是文本或者二进制内容
* **$server_socket**服务器可能会同时监听多个UDP端口，此参数可以指定使用哪个端口发送数据包

### 2.构建基本的UDP服务器
UDP服务器与TCP服务器不同，UDP没有连接的概念。启动Server后，客户端无需Connect，直接可以向Server监听的9502端口发送数据包。对应的事件为onPacket。
构建步骤：
1.创建swoole_server对象，注意**$sock_type类型选择SWOOLE_SOCK_UDP**
2.注册监听事件，对应事件为**Packet**，回调函数类型为**function ($serv, $data, $clientInfo)**
3.调用**$server->sendto**方法向客户端发送数据
4.启动服务器

{% codeblock %}
<?php

//创建Server对象，监听 0.0.0.0:9502端口，类型为SWOOLE_SOCK_UDP
$serv = new swoole_server("127.0.0.1", 9502, SWOOLE_PROCESS, SWOOLE_SOCK_UDP); 

//监听数据接收事件
$serv->on('Packet', function ($serv, $data, $clientInfo) {
    $serv->sendto($clientInfo['address'], $clientInfo['port'], "Server ".$data);
    var_dump($clientInfo);
});

//启动服务器
$serv->start(); 
{% endcodeblock %}
接下来我们上传文件到服务器并启动
![](/images/udpstart.png)
可以看到已经启动了，为了确保启动成功我们可以再开一个终端连接服务器查看进程
![](/images/udpstart1.png)
接下来我们使用网络调试助手NetAssist来测试，上次使用windows自带的telnet感觉太鸡肋了，这个小工具简单好用，我下载的是V3.8.1，其实都大同小异