---
title: Swoole(二)配置TCP服务器
tags:
  - php项目
  - swoole
date: 2018-02-15 01:55:02
categories: ItEassy
---
### 接来下我们来学习Swoole的基本函数，以此来配置一个TCP服务器
![](/images/swoole.jpg)
---
### 1.基本函数
#### 1.1 swoole_server：创建一个异步Server对象。
函数原型：$serv = new swoole_server(string $host, int $port = 0, int $mode = SWOOLE_PROCESS, int $sock_type = SWOOLE_SOCK_TCP);
* **$host**:指定监听的ip地址
IPv4使用 127.0.0.1表示监听本机，0.0.0.0表示监听所有地址
IPv6使用::1表示监听本机，:: (相当于0:0:0:0:0:0:0:0) 表示监听所有地址
* **$port**:监听的端口
如果$sock_type为UnixSocket Stream/Dgram，此参数将被忽略
监听小于1024端口需要root权限
如果此端口被占用server->start时会失败
* **$mode**:运行的模式
SWOOLE_PROCESS多进程模式（默认）
SWOOLE_BASE基本模式
* **$sock_type**:指定Socket的类型
支持TCP、UDP、TCP6、UDP6、UnixSocket Stream/Dgram 6种

#### 1.2 swoole_server->on：注册Server的事件回调函数。
函数原型：bool swoole_server->on(string $event, mixed $callback);
* **$event**:回调的名称
connect：建立连接时
receive：接收到数据时
close：关闭连接时
* **$callback**:回调的PHP函数，可以是函数名的字符串，类静态方法，对象方法数组，匿名函数。
connect：function ($serv, $fd){} $serv:服务器信息 $fd:客户端信息
receive：function ($serv, $fd, $from_id, $data){} $from_id:客户端ID $data:接收的数据
close：function ($serv, $fd){} $serv:服务器信息 $fd:客户端信息

#### 1.3 swoole_server->start：启动server，监听所有TCP/UDP端口。
函数原型：bool swoole_server->start()
启动成功后会创建:**Master进程+Manager进程+serv->worker_num个Worker进程**
- **Master进程**:主进程内有多个Reactor线程，基于epoll/kqueue进行网络事件轮询。收到数据后转发到worker进程去处理
- **Manager进程**:对所有worker进程进行管理，worker进程生命周期结束或者发生异常时自动回收，并创建新的worker进程
- **Worker进程**:对收到的数据进行处理，包括协议解析和响应请求。

启动失败会立即返回false
启动成功后将进入事件循环，等待客户端连接请求。start方法之后的代码不会执行
服务器关闭后，start函数返回true，并继续向下执行

#### 1.4 swoole_server->send：向客户端发送数据。
函数原型：bool swoole_server->send(int $fd, string $data, int $extraData = 0);
* **$data**:发送的数据
TCP协议最大不得超过2M，可修改buffer_output_size改变允许发送的最大包长度
UDP协议不得超过65507，UDP包头占8字节, IP包头占20字节，65535-28 = 65507
* UDP服务器使用**$fd保存客户端IP**，**$extraData保存server_fd和port**

发送成功会返回true
发送失败会返回false，调用$server->getLastError()方法可以得到失败的错误码

---

### 2.构建基本的TCP服务器
使用上述三个函数就可以构建出一个基本的TCP服务器了
{% codeblock %}
<?php

//创建Server对象，监听 0.0.0.0:9501端口
$serv = new swoole_server("0.0.0.0", 9501); 

//监听连接进入事件
$serv->on('connect', function ($serv, $fd) {  
    echo "Client: Connect.\n";
});

//监听数据接收事件
$serv->on('receive', function ($serv, $fd, $from_id, $data) {
    $serv->send($fd, "Server: ".$data);
});

//监听连接关闭事件
$serv->on('close', function ($serv, $fd) {
    echo "Client: Close.\n";
});

//启动服务器
$serv->start(); 
{% endcodeblock %}
之后我们把tcpServer.php上传到服务器上
![](/images/shangchuan.png)
在服务器端运行这个脚本
![](/images/kaishi.png)
我们可以再次连接服务器，通过查看进程确认脚本是否执行
![](/images/jincheng.png)
首先可以看到有一个进程-bash在使用一个子进程ps -ajft查看进程，这个bash就是我们连接服务器的进程
然后可以看到有一个进程-bash有三个子进程在执行，可以看出分别是Master进程(32680)、Manager进程(32681)和Worker进程(32683)
此时我们可以通过客户端使用网络工具来连接服务器（x为ip地址，y为端口号），我使用的是windows自带的telnet服务，具有TCP连接功能，如何开启请看[这里](https://jingyan.baidu.com/article/54b6b9c0813a362d593b4775.html)
![](/images/lianjie.png)
![](/images/fankui.png)
可以看到成功连接并且实现了向客户端返回数据的功能，若是无法正常连接，可能有如下三种原因
* 在Linux下，使用**netstat -an | grep 端口**或者**lsof -i:9501**，查看端口是否已经被打开处于Listening状态，若处于可以看看是什么哪个进程在使用这个端口，并用**kill -9 进程号(PID)**，结束这个进程重新开放端口
* 上一步确认后，检查防火墙问题，最好使用命令**systemctl stop firewalld.service**
* 注意服务器所使用的IP地址，如果是127.0.0.1回环地址，则客户端只能使用127.0.0.1才能连接上
* 若是阿里云服务器，记得在阿里云控制台中**安全组设置开放外界端口号**

这时我们的基本的tcp服务器就已经搭建好了，若需要关闭这个进程，可以使用**kill -9 进程号**关闭掉Master的父进程-bash(32618)即可，此时再使用**ps -ajft**和**netstat -an | grep 9501**，可看到进程结束并且端口不再处于监听状态即可

### 我们搭建基本tcp服务器的过程就结束了，接下来我们将会学习如何搭建别的类型服务器，如UDP、Web、Websocket、异步TCP，还有一些队列通信、信号触发、DNS查询、异步文件IO、异步mysql等。
![](/images/bai2.gif)
