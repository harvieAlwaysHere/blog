---
title: Redis(一)安装
tags:
  - php项目
  - Redis
date: 2018-03-04 15:48:12
categories: ItEassy
---
### Redis是完全开源免费的，遵守BSD协议，是一个高性能的key-value数据库。
![](/images/redis.jpg)
### Redis特点：
* 1.Redis支持**数据的持久化**，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
* 2.Redis不仅仅支持简单的**key-value类型的数据**，同时还提供**list，set，zset，hash**等数据结构的存储。
* 3.Redis支持数据的备份，即**master-slave模式(主从模式)的数据备份**。

### Redis在Linux下的安装与配置
#### 1.安装Redis：
1.1下载安装包：在[https://redis.io/download] 中选择你想要的版本下载压缩包并上传到服务器
1.2解压：tar -zxvf redis-3.2.11.tar.gz
1.3移动：cp -R redis-3.2.11 /usr/local/redis
1.4切换到移动后的地址：cd /usr/local/redis/redis-3.2.11
1.5安装：make install
完成后可看到如下
![](/images/redis10.png)
可进入目录下的**src资源文件夹**看到两个服务程序：**redis-cli（客户端） redis-server（服务端）**
![](/images/redis11.png)


#### 2.配置Redis
### 2.1测试Redis客户端(前台执行)：./redis-server
![](/images/redis12.png)
### 2.2添加环境变量：可全局执行命令
pwd ##查看当前目录，即使需要添加的环境变量的目录
vi /etc/profile ##打开**环境变量配置文件**
export PATH=$PATH:/usr/local/redis/redis-3.2.11/src ##在最底下添加这一行
![](/images/redis13.png)
source /etc/profile  ##立即生效
此时切换到别的目录可以看到可以使用redis命令
![](/images/redis14.png)
### 2.3修改Redis后台启动
cd /usr/local/redis/redis-3.2.11
其中的**redis.conf就是配置文件**
cp redis.conf redis_6379.conf ##拷贝配置文件
vi redis_6379.conf  ##编辑新的配置文件
将daemonize no => daemonize yes ##修改成以**守护进程(后台)**方式运行
![](/images/redis15.png)
### 2.4修改Redis启动脚本
cd /user/local/redis/redis-3.2.11/utils 
其中的**redis_init_script就是启动脚本**
cp redis_init_script redis_init_script_6379 ##拷贝启动脚本
vi redis_init_script_6379  ##编辑新的启动脚本
修改服务器路径、客户端路径、配置文件路径如下
![](/images/redis16.png)
### 2.5测试启动脚本
cd /usr/local/redis/redis-3.2.11/utils
./redis_init_script_6379 start  ##**运行启动脚本**
ps aux | grep redis  ##**查看进程** 查看是否启动Redis服务成功
![](/images/redis17.png)
### 2.6设置开机启动
vi /etc/re.local 添加
/usr/local/redis/redis-3.2.11/utils/redis_init_script_6379 start
### 2.7使用客户端访问（测试是否启动Redis服务成功）
redis-cli  ##**启动客户端连接Redis服务器**
\>ping
PONG  ##则启动服务成功
![](/images/redis18.png)
### 2.8客户端连接参数
**redis-cli连接参数**：-h 指定主机 -p 指定端口号 -a 指定密码
默认情况下无密码，使用密码有两种方式
1.**单次生效，通过命令配置**
* 设置密码：config set requirepass 123456
* 获取密码：config get requirepass
* 授权使用：auth 123456

2.**永久生效，通过修改配置文件**
* vi redis_6379.conf
* 取消'requirepass'的注释，加上密码，如requirepass 123456

Tips:无论单次还是永久密码，启动密码后，**无密码也可以连接，但无权限进行操作**
![](/images/redis19.png)


### 3.总结
**3.1启动Redis服务**：
cd /usr/local/redis/redis-3.2.11/utils
./redis_init_script_6379 start
**3.2停止Redis服务**：
redis-cli
\>shutdown
**3.2客户端连接Redis服务**：
redis-cli




