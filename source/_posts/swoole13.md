---
title: Swoole(十三)DNS查询
tags:
  - php项目
  - swoole
date: 2018-02-20 18:17:48
categories: ItEassy
---
### Swoole有(异步)DNS查询功能(我也不知道为什么集成了)
![](/images/swoole.jpg)

---
### 1.（异步）将域名解析为IP地址：swoole_async_dns_lookup
函数原型：swoole_async_dns_lookup($domainName,callback function($host,$ip){});
**$domainName**：传入的域名
**DNS查询完成**：自动回调指定的**callback函数**。
**DNS查询失败**：比如域名不存在，**回调函数传入的$ip为空**
调用此函数是**非阻塞的**，调用会立即返回。将向下执行后面的代码。

### 2.执行DNS查询
{% codeblock %}
swoole_async_dns_lookup("www.baidu.com", function($host, $ip){
    echo "{$host} : {$ip}\n";
});
{% endcodeblock %}
脚本在服务器运行，可以看到执行成功
![](/images/swoole13.png)


