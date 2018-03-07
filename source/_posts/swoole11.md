---
title: Swoole(十一)进程信号触发器
tags:
  - php项目
  - swoole
date: 2018-02-20 17:29:28
categories: ItEassy
---

### Swoole具备进程管理模块，用来代替PHP的pcntl拓展。
![](/images/swoole.jpg)
---
### 1.设置异步信号监听：swoole_process::signal
函数原型：bool swoole_process::signal(int $signo, callable $callback);
此方法**基于signalfd和eventloop**是**异步IO**，不能用于同步程序中
**同步阻塞**的程序可以使用**pcntl扩展提供的pcntl_signal**
**$callback**如果为**null**，表示**移除信号监听**
如果已设置了此信号的回调函数，**重新设置时会覆盖历史设置**
---
### 2.高精度定时器：swoole_process::alarm
高精度**定时器**，是操作系统setitimer系统调用的封装，可以设置**微秒级别的定时器**。定时器会**触发信号**，需要与swoole_process::signal（**异步**）或pcntl_signal（**同步**）配合使用。
函数原型：function swoole_process::alarm(int $interval_usec, int $type = ITIMER_REAL) : bool
**$interval_usec**：定时器**间隔时间**，单位为**微秒**。如果为**负数表示清除定时器**
**$type**：定时器**类型**
* 0 表示为真实时间,触发SIGALAM信号
* 1 表示用户态CPU时间，触发SIGVTALAM信号
* 2 表示用户态+内核态时间，触发SIGPROF信号

---
### 3.创建定时器及信号监听函数
{% codeblock %}
<?php

//触发函数 异步执行 达到条件停止
swoole_process::signal(SIGALRM,function(){
	//计时器 用于停止定时器触发信号
	static $i=0;  
	echo "hello!\n";
	$i++;
	if($i>10){
		//触发十次 清除定时器
		swoole_process::alarm(-1);
	}
});

//定时信号
swoole_process::alarm(100*1000);
{% endcodeblock %}
将脚本上传至服务器并运行，可以看到
![](/images/swoole11.png)
成功发送了10次信号并触发后清除定时器停止程序
