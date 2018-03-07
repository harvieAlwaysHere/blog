---
title: Swoole(十)进程队列通信
tags:
  - php项目
  - swoole
date: 2018-02-20 17:25:28
categories: ItEassy
---
### Swoole具备进程管理模块，用来代替PHP的pcntl拓展。
![](/images/swoole.jpg)

---
### 1.投递数据到消息队列：swoole_process->push
函数原型：bool swoole_process->push(string $data);
**$data**：投递的数据，长度受限与**操作系统内核参数**的限制。默认为8192，最大不超过65536
默认模式下（阻塞模式）：**队列已满**，push方法会**阻塞等待**
非阻塞模式下：**队列已满**，push方法会立即**返回false**

---
### 2.从队列中提取数据：swoole_process->pop
函数原型：string swoole_process->pop(int $maxsize = 8192);
**$maxsize**：表示获取数据的最大尺寸，默认为8192
操作成功会**返回提取到的数据内容**，失败返回false
默认模式下：**队列中没有数据**，pop方法会**阻塞等待**
非阻塞模式下：**队列中没有数据**，pop方法会立即**返回false**，并设置**错误码为ENOMSG**

---
### 3.回收结束运行的子进程：swoole_process::wait
子进程结束必须要执行wait进行回收，否则子进程会变成**僵尸进程**
函数原型：array('code' => 0, 'pid' => 15001, 'signal' => 15) swoole_process::wait(bool $blocking = true);
**$blocking**：可以指定**是否阻塞等待**，默认为阻塞
**操作成功**会返回**一个数组包含子进程的PID、退出状态码、被哪种信号KILL**
如果在**异步信号回调中执行wait**
{% codeblock %}
swoole_process::signal(SIGCHLD, function($sig) {
  //必须循环执行wait直到返回false
  while($ret =  swoole_process::wait(false)) {
      echo "PID={$ret['pid']}\n";
  }
});
{% endcodeblock %}

---
### 4.启用消息队列作为进程间通信：swoole_process->useQueue
函数原型：bool swoole_process->useQueue(int $msgkey = 0, int $mode = 2);
**$msgkey**：消息队列的key，默认会使用ftok(__FILE__, 1)作为KEY
**$mode**：通信模式，默认为2，表示**争抢模式**，所有创建的子进程都会从队列中取数据

---
### 5.退出子进程：swoole_process->exit
函数原型：int swoole_process->exit(int $status=0);
**$status**是退出进程的状态码，如果**为0**表示**正常结束**，会继续执行PHP的shutdown_function，其他扩展的清理工作。
$status**不为0**，表示**异常退出**，会立即终止进程。不再执行PHP的shutdown_function，其他扩展的清理工作。
在**父进程**中，执行**swoole_process::wait**可以得到**子进程退出的事件和状态码**。

---
### 6.创建进程队列并通信
{% codeblock %}
<?php

//进程池 存储进程
$workers = [];  
//进程数量
$worker_num = 2;

//创建并启动进程
for($i=0;$i<$worker_num;$i++){
	//创建新进程，注意第三个参数为false，不启动pipe通信
	$process = new swoole_process('doProcess',false,false);  
	//启用消息队列作为进程间通信
	$process->useQueue();
	$pid = $process->start();  //启动进程并获取进程ID
	$workers[$pid] = $process;  //将进程存入进程池
}

//编写进程执行函数
function doProcess(swoole_process $worker){
	//从主进程(master)中获取数据
	$recv = $worker->pop();
	echo "Worker(".$worker->pid.")From Master: ".$recv."\n";
	sleep(2);
	//退出子进程 0表示正常结束
    $worker->exit(0);
}

//主进程向子进程添加数据
foreach ($workers as $process) {
	$process->push("hello worker[".$process->pid."]\n");
}

//等待子进程结束回收资源
for($i = 0; $i < $worker_num; $i++)
{
	//等待执行完成
    $ret = swoole_process::wait();
    $pid = $ret['pid'];
    //释放进程池中的进程资源
    unset($workers[$pid]);
    echo "Worker Exit, PID=".$pid.PHP_EOL;
}
{% endcodeblock %}
将PHP脚本上传至服务器并运行，可以看到
![](/images/swoole10.png)
进程确实pop到了主进程push的数据，但是不知道为什么主进程push数据的时候识别的子进程的ID打印出来是错误的，可以看到子进程的PID分别是9922和9921，回收进程时也是这两个ID，可是接收到主进程push的数据时的PID确实9918和9919