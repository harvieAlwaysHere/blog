---
title: Swoole(十四)异步文件读取
tags:
  - php项目
  - swoole
date: 2018-02-20 18:17:51
categories: ItEassy
---
### Swoole增加了异步文件读写，异步DNS，异步Http/WebSocket客户端等特性。开发纯异步非阻塞IO的程序时，不能使用PHP自带的网络客户端，如curl、file_get_contents、stream、sockets、mysql、redis。
![](/images/swoole.jpg)

---
### 1.(小文件)异步读取文件内容：swoole_async_readfile
提供了两种风格
函数风格：swoole_async_readfile(string $filename, mixed $callback);
面向对象风格：Swoole\Async::readFile(string $filename, mixed $callback);
**$filename**：路径下的文件名
**$callback**：由于是**异步非阻塞**，数据读取完毕后会**立即调用**指定的回调函数
Tips：swoole_async_readfile会**将文件内容全部复制到内存**，所以**不能用于大文件的读取**
如果要读取**超大文件**，请使用**swoole_async_read函数**

---
### 2.(大文件)异步读取文件内容：swoole_async_read
函数原型：bool swoole_async_read(string $filename, mixed $callback, int $size = 8192, int $offset = 0);
**$filename**：路径下的文件名
**$callback**：回调函数接受两个参数**bool callback(string $filename, string $content);**，$filename-文件名称，$content-读取到的**分段内容**，**如果内容为空，表明文件已读完**，在回调函数中可通过**return true/false**，来**控制继续读下一段内容/停止读取并关闭文件**。
**$size**：读取的分段内容的大小，单位字节
Tips：此函数与swoole_async_readfile不同，它是**分段读取**，可以用于**读取超大文件**。每次只读$size个字节，不会占用太多内存。

### 3.两种读取方式测试
{% codeblock %}
<?php

echo "swoole_async_readfile \n";

//readFile读取
swoole_async_readfile(__DIR__."/testFile.txt", function($filename, $content) {
     echo "swoole_async_readfile-[$filename] : $content \n";
});

echo "swoole_async_read \n";

//read读取
swoole_async_read(__DIR__."/testFile.txt", function($filename, $content) {
     if($content){
     	echo "swoole_async_read-[$filename] : $content \n";
     	return true;
     }else{
     	return false;
     }
     
});
{% endcodeblock %}
上传脚本和测试文本到服务器可以看到
![](/images/swoole140.png)
由于是**异步读取**，两个echo都在文件读取之前输出出来了，之后就是两个文件读取内容的打印






