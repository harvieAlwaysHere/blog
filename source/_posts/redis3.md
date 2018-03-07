---
title: Redis(三)PHP操作Redis
tags:
  - php项目
  - Redis
date: 2018-03-04 15:49:05
categories: ItEassy
---
### 使用PHP拓展操作Redis
![](/images/redis.jpg)


### 下载安装PHP-Redis拓展
1.下载压缩文件包[https://github.com/phpredis/phpredis]
2.将文件上传到服务器并解压
unzip phpredis-develop.zip
若出现-bash: unzip: command not found
则需要安装unzip
![](/images/redis30.png)
3.进入并phpize处理
cd phpredis-develop
phpize
![](/images/redis31.png)
4.指定配置文件
若不知道php-config在哪里，可以使用命令查找
find / -name "php-config"
![](/images/redis33.png)
指定配置文件路径
./configure --with-php-config=/usr/bin/php-config
5.编译和安装
make && make install
成功后会显示
![](/images/redis32.png)
6.修改PHP配置文件php.ini添加.so拓展文件
查找php.ini文件并进入vi编辑模式
![](/images/redis34.png)
类似添加Swoole拓展一样，在底下添加
extension = /usr/lib64/php/modules/redis.so
![](/images/redis35.png)
7.查看是否安装成功
php -m
![](/images/redis36.jpg)

### 使用PHP拓展操作Redis
简单连接、授权密码、设置字符串、读取字符串测试
![](/images/redis36.png)
在服务端运行该脚本可看到
![](/images/redis27.png)

修改redis_6379.conf配置文件
![](/images/redis38.png)
将bind语句加上#注释掉
![](/images/redis37.png)
在redis3.2后增加了保护模式，在这个模式下也不允许外网访问，需要设置成protected-mode no
![](/images/redis390.png)
重启Redis
![](/images/redis39.png)
<font color='red'>**[三个小时踩了一个坑]**</font>
我用http://39.108.210.229/redis.php 无法访问，于是我在redis.php文件上加上
ini_set('display_errors',1);
ini_set('display_startup_errors',1);
error_reporting(-1);
打印出错误信息，发现错误信息为
Fatal error: Uncaught Error: Class 'Redis' not found in /var/www/html/redis.
证明找不到这个类，可是明明已将拓展添加到php.ini中了，于是我在cli下执行php -m可以看到有redis拓展，在web下访问phpinfo()，**发现竟然没有redis拓展**，难怪找不到Redis类
于是我百度了下**php -m和phpinfo()不一致**，发现可能是cli加载的配置文件和web加载的php配置文件不同，于是在cli下输入which php发现与phpinfo()出现的版本相同，那看来cli下的php和web下的php是相同的，但是为什么拓展不同呢，最后才明白，**因为没有重新启动httpd服务器**，重新启动后phpinfo()中就有redis拓展了，访问http://39.108.210.229/redis.php 也成功打印出了数据
![](/images/redis391.png)