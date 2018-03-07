---
title: Swoole(九)同步/异步进程
tags:
  - php项目
  - swoole
date: 2018-02-19 17:40:20
categories: ItEassy
---
### Swoole具备进程管理模块，用来代替PHP的pcntl拓展。
Process进程是系统中十分昂贵的资源，创建进程消耗很大，可以使用**vmstat**指令查看系统每秒进程切换次数
![](/images/swoole.jpg)

---
### 1.PHP自带pcntl拓展存在很多不足
* pcntl没有提供**进程间通信**的功能
* pcntl**不支持重定向标准输入和输出**
* pcntl只提供了**fork这样原始的接口**，容易使用错误
* swoole_process提供了比pcntl更强大的功能，更易用的**API**，使PHP在多进程编程方面更加轻松。

---
### 2.swoole_process提供了如下特性
* swoole_process提供了**基于unixsock的进程间通信**，使用很**简单**只需**调用write/read或者push/pop**即可
* swoole_process**支持重定向标准输入和输出**，在子进程内echo不会打印屏幕，而是**写入管道**，读键盘* 输入可以重定向为**管道读取数据**
* 配合swoole_event模块，创建的PHP子进程可以**异步的事件驱动模式**
swoole_process提供了**exec接口**，创建的进程可以执行其他程序，与原**PHP父进程之间可以方便的通信**

---
### 3.创建进程：new swoole_process()
函数原型：swoole_process::__construct(callable $function, $redirect_stdin_stdout = false, $create_pipe = true);
参数：
* **callable $function**：**子进程创建成功后要执行的函数**，底层会自动将函数保存在**对象的callback属性**上，若要更改执行函数则可赋值新的函数在对象的callback属性。
* **$redirect_stdin_stdout**：**重定向子进程的标准输入输出**，**启动**此选项后，子进程输出内容不是打印到屏幕而是**写入主进程管道**，读取键盘输入变成**从管道中读取数据**，默认为**阻塞读取**。
* **$create_pipe**：**是否创建管道**，启动重定向后此项强制为1/true，如果子进程内没有进程间通信科设置为false。
* 管道类型：0/false => 不创建管道，1/true => 管道类型将设置为SOCK_STREAM(流式)，2 => 管道类型将设置为SOCK_DGRAM(数据包)

---
### 4.启动进程：swoole_process->start
执行**fork系统调用**，启动进程
函数原型：int swoole_process->start();
返回参数：创建成功返回**子进程的PID**，创建失败返回false
启动后可用属性：
* **$process->pid**属性为子进程的PID
* **$process->pipe**属性为管道的文件描述符

---
### 5.向管道写入数据：swoole_process->write
函数原型：int swoole_process->write(string $data);
参数：**$data**的长度在Linux系统下最大不超过8K，MacOS/FreeBSD下最大不超过2K
父/子进程调用write，子/父进程可调用read接收数据
同步模式：进程内未使用任何异步IO，则管道为**同步阻塞**模式，如果缓冲区满了，将阻塞直到write操作完成。如**Task**进程就是同步阻塞模式。
异步模式：进程内使用了异步IO，如**swoole_event_add(进程事件)**,进程内wirte操作变成异步模式，swoole底层会监听可写事件，自动完成管道写入。

---
### 6.从管道中读取数据：swoole_process->read
函数原型：function swoole_process->read(int $buffer_size=8192) : string | bool;
* **$buffer_size**是缓冲区的大小，默认为8192，最大不超过64K
* **管道类型为DGRAM数据报(2)**时，read可以读取完整的一个数据包
* **管道类型为STREAM(1/true)**时，read是流式的，需要自行处理包完整性问题
* 读取**成功**返回**二进制数据字符串**，读取**失败**返回**false**

---
### 7.添加进程事件：异步IO，swoole_event_add
swoole_event_add函数用于将一个socket加入到底层的reactor事件监听中。此函数可以用在Server或Client模式下。
函数原型：bool swoole_event_add(int $sock, mixed $read_callback, mixed $write_callback = null, int $flags = null);
* **int $sock**：可以为一下三种类型
**1.int**，就是**文件描述符**,包括swoole_client的socket,以及第三方扩展的socket（比如mysql）
**2.stream资源**，就是**stream_socket_client/fsockopen创建的资源**
**3.sockets资源**，就是sockets扩展中**socket_create创建的资源**，需要在编译时加入 ./configure --enable-sockets
* **mixed $read_callback**：可读回调函数
* **mixed $write_callback**:可写事件回调，可以是字符串函数名、对象+方法、类静态方法或匿名函数，当此socket可读时回调指定的函数。
* **int $flags**：事件类型的掩码，可选择关闭/开启可读可写事件，如SWOOLE_EVENT_READ，SWOOLE_EVENT_WRITE，或者SWOOLE_EVENT_READ | SWOOLE_EVENT_WRITE

---
### 8.创建同步进程
{% codeblock %}
<?php

//创建进程对应的执行函数
function doProcess(swoole_process $worker){
	var_dump($worker);
	echo "\n";
	echo "PID : ".$worker->pid."\n";
	sleep(5);
}

//创建进程 没有启动重定向
$process = new swoole_process("doProcess");
$pid = $process->start();

//匿名函数创建进程(启动重定向)
$process = new swoole_process(function (swoole_process $process) {
    $process->write('Hello');
}, true);
$process->start();
sleep(10);
echo $process->read();  //从管道输出

//等待结束，关闭子进程(若不等待结束，容易出现僵尸进程)
swoole_process::wait();
{% endcodeblock %}
将脚本上传至服务器并启动，可以看到，**无重定向**打印了$worker的内容和PID，**重定向**输出了Hello
![](/images/swoole9.png)

---
### 9.创建异步进程
因为子进程会继承父进程的内存和IO句柄，所以如果父进程要创建多个子进程，**务必要等待创建完毕后**再使用swoole_event_add/异步swoole_client/定时器/信号等异步IO函数。
{% codeblock %}
<?php

//进程池 存储进程
$workers = [];  
//进程数量
$worker_num = 3;

//创建并启动进程
for($i=0;$i<$worker_num;$i++){
	$process = new swoole_process('doProcess');  //创建单独新进程
	$pid = $process->start();  //启动进程并获取进程ID
	$workers[$pid] = $process;  //将进程存入进程池
}

//编写进程执行函数
function doProcess(swoole_process $process){
	$process->write("PID : (".$process->pid.")\t CallbackFunction(".$process->callback .")".PHP_EOL);
    echo "Write Msg : PID(".$process->pid.")\t CallbackFunction(".$process->callback .")".PHP_EOL;
}

//添加进程事件 向每个子进程添加需要执行的事件
foreach ($workers as $process) {
	//子进程也会包含此事件
	swoole_event_add($process->pipe,function($pipe) use($process){
		$data = $process->read();
		echo "Receive Msg : ".$data.PHP_EOL;
	});
}
{% endcodeblock %}
将脚本上传至服务器并启动，可以看到成功将PID和Callback函数写入管道并且读出来了
![](/images/swoole91.png)