---
title: Swoole(七)异步TCP服务器
tags:
  - php项目
  - swoole
date: 2018-02-18 19:14:06
categories: ItEassy
---
### Swoole提供了异步任务处理的功能，可以投递一个异步任务到TaskWorker进程池中执行，不影响当前请求的处理速度。
![](/images/swoole.jpg)
在Server程序中如果需要执行很耗时的操作，比如一个聊天服务器发送广播，Web服务器中发送邮件。如果直接去执行这些函数就会阻塞当前进程，导致服务器响应变慢。

---
### 1.代码讲解
1.创建TCP服务器：$serv = new swoole_server("0.0.0.0", 9501);
2.设置**异步任务的工作进程数**
{% codeblock %}
$serv->set(array('task_worker_num' => 4));
{% endcodeblock %}
3.**onReceive**事件，用来**投递异步任务**，同时**获取此次异步任务的ID**
{% codeblock %}
//投递异步任务
$serv->on('receive', function($serv, $fd, $from_id, $data) {
    //获取此次异步任务的ID
    $task_id = $serv->task($data);
});
{% endcodeblock %}
4.**onTask**事件，用来**处理异步任务**，同时**返回任务执行的结果**
{% codeblock %}
$serv->on('task', function ($serv, $task_id, $from_id, $data) {
    //返回任务执行的结果
    $serv->finish("$data -> OK");
});
{% endcodeblock %}
5.**onFinish**事件，用来**处理异步任务的结果**
{% codeblock %}
//处理异步任务的结果
$serv->on('finish', function ($serv, $task_id, $data) {
    
});
{% endcodeblock %}
6.启动服务器，$serv->start();

---
### 2.代码运行
上传脚本代码至服务器并运行，再开启一个终端可以看到已经有4个额外的worker进程在运行
![](/images/swoole7.png)
我们把这个终端当作**TCP客户端**采用TCP方式连接服务器测试，并发送消息
![](/images/swoole71.png)
此时在另一个终端即**TCP服务器端**可以看到如下
![](/images/swoole72.png)
