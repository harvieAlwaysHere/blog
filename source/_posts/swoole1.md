---
title: Swoole(一)安装
tags:
  - php项目
  - swoole
date: 2018-02-10 00:29:04
categories: ItEassy
---
### 说到PHP异步网络通信框架，相信PHPer脑海里第一个浮现的便是Swoole。
![](/images/swoole.jpg)
* 商业应用：IM聊天、战旗TV、虎牙直播、YY语音等。
* 应用领域：互联网、网络通信、企业软件、云计算、网络游戏、物理网等。
* 特点：
1.异步、并行、高性能
2.纯C编写，接近应用底层
3.php扩展
* 应用：
1.异步多线程服务器及客户端
2.异步MySQL、Redis、数据库连接池、任务队列
3.http/websocket服务器/客户端
4.异步文件读写
5.Swoole2.0支持协程
---
### 安装Swoole
大致可分成3个步骤:
1.搭建Linux操作系统
2.安装环境依赖(php,gcc,httpd，pecl)
3.安装Swoole并修改php.ini

---
#### Part1:搭建Linux操作系统
查看[官方文档](https://wiki.swoole.com/wiki/page/7.html)可知Swoole并不支持Windows系统，所以我们这里搭建一个**CentOS 7.3 64位**的操作系统，我是使用阿里云云服务器 ECS搭建的，使用系统盘安装即可，安装完毕之后可以远程连接进入云服务器中的Linux系统中
![](/images/aliyun.png)
Tips:如果想在Windows下便捷操作云服务器的系统，推荐使用SSH连接软件，这里我使用的是**SSH Secure Shell Client3.2.9**，安装完成后有两个工具可以使用，分别对应Linux系统的**命令行界面**和Linux**文件系统的GUI界面**的，十分便捷客观，提供工作效率。可参考[这篇博客](http://blog.csdn.net/fengqingtao2008/article/details/78018807)安装使用。
![](/images/SSH.png)
连接成功后的界面为
![](/images/connect.png)
<br>
#### Part2:安装环境依赖(php,gcc,httpd，pecl)
在CentOS中使用yum**安装php7**，由于初始yum所安装的php版本是5.\*的，我们首先要获取**php7的yum源**
{% codeblock %}
//1.安装epel-release
yum -y install epel-release 
      
//2.获取php7的yum源
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

//3.安装php7
yum install php70w

//4.验证安装
php -v
{% endcodeblock %}
安装完成后可看到php版本
![](/images/php.png)
之后我们要**安装gcc和httpd**
{% codeblock %}
yum install httpd gcc
{% endcodeblock %}
![](/images/gcc.png)
最后我们要**安装pecl**，因为swoole项目已收录到PHP官方扩展库，可直接使用pecl安装swoole
{% codeblock %}
wget http://pear.php.net/go-pear.phar
php go-pear.phar
{% endcodeblock %}
![](/images/pecl.png)
<br>
---
#### Part3:安装Swoole并修改php.ini
{% codeblock %}
sudo pecl install swoole
{% endcodeblock %}
会**报错**如下，别着急
![](/images/baocuo.png)
输入
{% codeblock %}
vi `which pecl`
{% endcodeblock %}
进入Linux的Vim编辑模式，一直向下查找找到这个文件的最后一行
![](/images/zhaodao.png)
按I进入Vim的插入模式，之后将-n标志去掉，按ESC进入Vim的退出(末行)模式，输入[:wq!]保存并退出
![](/images/wq.png)
之后安装 php-devel
{% codeblock %}
sudo yum install php70w-devel
{% endcodeblock %}
再次安装swoole
{% codeblock %}
sudo pecl install swoole
{% endcodeblock %}
成功安装！
![](/images/success.png)
根据提示要添加"extension=swoole.so"在php配置文件php.ini中，我这里由于对于Vim的操作并不熟悉，我直接通过SSH软件将php.ini文件拉取到本地之后修改再上传到服务器
php.ini一般在/etc目录下
![](/images/ini.png)
拉取到本地后添加"extension=swoole.so"语句，随意在任何两个配置语句中增加
![](/images/add.png)
之后通过语句[php -m]可查看php拓展安装情况，如果看到swoole则安装成功！
![](/images/ok.png)
---
### 如果你实践完成了本教程，意味着你对于Linux和Windows系统的协同开发、php拓展安装已经了解了基本情形了，接下来我们将使用swoole开发php应用，敬请期待。
![](/images/bai.gif)