---
title: Swoole(八)同步TCP客户端
tags:
  - php项目
  - swoole
date: 2018-02-18 20:06:51
categories: ItEassy
---
### 创建一个TCP的同步客户端，可用于连接我们之前的实例TCP服务器
![](/images/swoole.jpg)

---
### 1.函数讲解
1.**建立TCP客户端对象**：$client = new swoole_client(**SWOOLE_SOCK_TCP**);
2.**连接到服务器**
使用客户端对象的**connect**方法，参数分别为服务器$ip、服务器端口号$port和连接超时时间$time
{% codeblock %}
$client->connect('127.0.0.1', 9501, 0.5);
{% endcodeblock %}
TCP需要进行**3次握手**，所以connect至少需要**3次网络传输**过程
![](/images/tcp_syn.png)
3.**向服务器发送数据**
使用客户端对象的**send**方法，参数为字符串
{% codeblock %}
$client->send("hello world");
{% endcodeblock %}
在发送**少量数据**时$client->send都是可以**立即返回**的。发送**大量数据**时，socket缓存区可能会塞满，send操作会**阻塞**。
4.**从服务器接收数据**
使用客户端对象的**recv**方法
{% codeblock %}
$data = $client->recv();
{% endcodeblock %}
recv操作会**阻塞等待服务器返回数据**，recv耗时等于服务器处理时间+网络传输耗时之合。
5.**关闭连接**
{% codeblock %}
$client->close();
{% endcodeblock %}

这个客户端是**同步阻塞**的，connect/send/recv 会**等待IO完成后再返回**。同步阻塞操作并不消耗CPU资源，IO操作未完成当前进程会自动转入sleep模式，当IO完成后操作系统会唤醒当前进程，继续向下执行代码。

---
### 2.代码构建
{% codeblock %}
<?php

$client = new swoole_client(SWOOLE_SOCK_TCP);

//连接到服务器
if (!$client->connect('0.0.0.0', 9501, 0.5))
{
    die("connect failed.");
}
//向服务器发送数据
if (!$client->send("hello world"))
{
    die("send failed.");
}
//从服务器接收数据
$data = $client->recv();
if (!$data)
{
    die("recv failed.");
}
echo "recv data is :".$data."\n";
//关闭连接
$client->close();
{% endcodeblock %}
---
### 3.代码演示
首先我们打开一个终端，执行一个tcp服务器的脚本
![](/images/swoole8.png)
之后我们上传tcp客户端脚本至服务器并运行，可以看到
![](/images/swoole81.png)
客户端连接服务器并发送数据成功，而且还接受到了服务器返回的数据，看看服务器端的反应
![](/images/swoole82.png)
证明客户端连接成功并且断开连接成功了