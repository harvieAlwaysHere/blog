---
title: Swoole(十二)锁机制
tags:
  - php项目
  - swoole
date: 2018-02-20 18:17:41
categories: ItEassy
---
### Swoole针对内存的其他提供了一系列的锁机制
![](/images/swoole.jpg)
swoole_lock类支持5种锁的类型：
* **文件锁 SWOOLE_FILELOCK**
* **读写锁 SWOOLE_RWLOCK**
* **信号量 SWOOLE_SEM**
* **互斥锁 SWOOLE_MUTEX**
* **自旋锁 SWOOLE_SPINLOCK**
Tips：之前学习单例模式算法的时候，为了防止两个进程创建两个单例，就需要使用锁机制，C++/C#有**private static object syncObj = new object();**，JAVA中有**synchronized(Singleton2.class){}**，没想到PHP也有Swoole扩展的锁机制。

---
### 1.创建锁对象：$lock = new swoole_lock();
函数原型：swoole_lock->__construct(int $type, [string $lockfile]);
**$type**：锁的类型，具体为以上五种锁
**$lockfile**：当类型为**SWOOLE_FILELOCK(文件锁)**时必须传入，**指定文件锁的路径**
注意**每一种类型的锁支持的方法都不一样**，如读写锁、文件锁可以支持$lock->lock_read()。
另外**除文件锁外，其他类型的锁必须在父进程内创建**，这样fork出的子进程之间才可以互相争抢锁。

---
### 2.加锁：swoole_lock->lock
函数原型：bool $lock->lock();
如果有**其他进程持有锁**，那这里将**进入阻塞**，直到持有锁的进程unlock。

---
### 3.释放锁：swoole_lock->unlock
函数原型：bool $lock->unlock();
解锁成功返回true。

---
### 4.创建一个互斥锁并通过主进程和子进程演示
{% codeblock %}
<?php

//创建锁对象（互斥锁）
$lock = new swoole_lock(SWOOLE_MUTEX);
echo "[Master]create mutex lock\n";
//主进程 加锁
$lock->lock();
//创建子进程
if(pcntl_fork() > 0){
	//创建成功则主进程解锁
	sleep(1);
	$lock->unlock();
}else{
	//创建失败则子进程等待主进程解锁后子进程再加锁进行自身的操作
	echo "[Child] Wait Lock\n";
	$lock->lock();
    echo "[Child] Get Lock\n";
    $lock->unlock();
    exit("[Child] exit\n");
}
echo "[Master]release lock\n";
unset($lock);
sleep(1);
echo "[Master]exit\n";
{% endcodeblock %}
运行脚本在服务器上，可以看到
![](/images/swoole12.png)
首先主进程创建互斥锁并上锁(create mutex lock)
此时子进程创建失败只能等待(wait lock)
等到主进程释放锁(release lock)
主进程释放锁后，子进程首先加锁(get lock)
之后子进程做自己的事情，完成后解锁退出(exit)
此时主进程再退出(exit)



