---
title: Swoole(十五)异步文件写入
tags:
  - php项目
  - swoole
date: 2018-02-20 18:17:56
categories: ItEassy
---
### Swoole增加了异步文件读写，异步DNS，异步Http/WebSocket客户端等特性。开发纯异步非阻塞IO的程序时，不能使用PHP自带的网络客户端，如curl、file_get_contents、stream、sockets、mysql、redis。
![](/images/swoole.jpg)

---
### 1.(小文件)异步写文件：swoole_async_writefile
提供了两种风格
函数风格：swoole_async_writefile($filename, $fileContent, function($filename){}, $flags = 0);
面向对象风格：Swoole\Async::writeFile(string $filename, string $fileContent, callable $callback = null, int $flags = 0)
**$filename**：文件名称，须有**可写权限**，文件不存在会自动创建，打开文件失败会返回false
**$fileContent**：写入文件的内容，**最大可写入4M**
**$callback**：写入成功的回调函数，**可选**
**flags**：写入选项，可以使用**FILE_APPEND**表示追加到文件末尾
Tips：Linux**原生异步IO**不支持FILE_APPEND，并且写入的内容长度必须为4096的整数倍，否则底层会自动在末尾填充0

---
### 2.(大文件)异步写文件：swoole_async_write
函数原型：bool swoole_async_write(string $filename, string $content, int $offset = -1, mixed $callback = NULL);
**$filename**：文件名称
**$content**：写入文件的内容，因为是**分段写入**，所以无限制
**$offset**：写入方式，-1为追加写入到文件末尾
**$callback**：写入成功的回调函数，可选
swoole_async_write是**分段写的**。不需要一次性将要写的内容放到内存里，所以**只占用少量内存**。swoole_async_write通过**传入的offset参数来确定写入的位置**。

### 3.两种写入方式测试
将脚本上传到服务器运行可以看到
![](/images/swoole150.png)
我们用vim打开写入的文件看看内容
![](/images/swoole151.png)
可以看到由于是**异步写入**，write比writeFile2的写入还要早执行，但是三个写入函数都执行成功了，一个是直接写入，一个是追加写入，还有一个是大文件的写入
