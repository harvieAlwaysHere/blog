---
title: Swoole(十六)实现一个长连接的网络聊天室
tags:
  - php项目
  - swoole
date: 2018-02-21 19:09:56
categories: ItEassy
---

### 这次我们首先一个类似IM的网络聊天室，服务器端使用websocket，客户端使用JS连接。
![](/images/swoole.jpg)

### 1.Websocket服务器
首先将服务器端代码写好
{% codeblock %}
<?php

//创建Websocket服务器
$ws = new swoole_websocket_server("0.0.0.0", 9501);

//on监听事件函数 open/message/close
$ws->on('open', function ($ws, $request) {
    echo "user".$request->fd."in\n";
    //将新用户存在全局变量里
    $GLOBALS['fd'][$request->fd]['id'] = $request->fd;
    $GLOBALS['fd'][$request->fd]['name'] = "noName";
});

$ws->on('message', function ($ws, $request) {
    //若接受的数据中含#name# 则为设置用户名
    if(strstr($request->data, "#name#")){
    	//将接受到的数据中的#name#替换成空白后存入相应的全局变量中
    	$GLOBALS['fd'][$request->fd]['name'] = str_replace("#name#", '', $request->data);
    }else{//若接受的数据中不含#name#, 则为用户发送消息
    	//编辑消息格式
    	$msg =  $GLOBALS['fd'][$request->fd]['name'].":".$request->data."\n";
    	//向所连接的每个客户端发送消息
    	foreach ($GLOBALS['fd'] as $client) {
    		//使用push推送消息
    		$ws->push($client['id'],$msg);
    	}

    }
});

$ws->on('close', function ($ws, $request) {
     echo "user".$request->fd."out\n";
     //删除用户信息
     unset( $GLOBALS['fd'][$request->fd]);
});

//启动服务器
$ws->start();
{% endcodeblock %}

### 2.配置LAMP环境
LP我们已经具备了，现在配置AM环境
**2.1 安装Apache**
查询是否有Apache安装包：yum list httpd
若有直接安装（如我的有x86_64版本的）:yum install httpd.x86_64
若没有则使用yum安装Apache：yum -y install httpd
设置开机启动：chkconfig --levels 235 httpd on
启动Apache：service httpd start
(重启Apache：service httpd restart)
查询Apache是否启动：ps -ef|grep httpd
 <font color='red'>[Apache默认网站目录/var/www/html]</font>
测试是否访问正常：cd /var/www/html/
vim index.php
<?php
   phpinfo();
?>
此时可访问localhost或者使用外网IP访问，出现phpinfo信息则正常
![](/images/swoole160.png)

**2.2 安装Mysql**
由于CentOS7默认是安装**Maridb**，但是不用担心，因为Maridb是mysql的一个分支且支持任何mysql语句，所以我们安装maridb，命令和操作都照常使用mysql的就可以拉，具体maridb和mysql的渊源可以看看[知乎这里](https://www.zhihu.com/question/41832866)
安装Maridb：yum install -y mariadb-server
启动Maridb： service mariadb start
（停止和重启服务是 service+服务名+stop/restart）
查询是否启动：ps -ef|grep mariadb （此时可查询mysql服务，也可以查询出结果）
设置开启启动：chkconfig --levels 235 mariadb on
设置root账户密码：mysql_secure_installation 
Tips:一开始提示输入原密码直接回车就好，因为原密码为空，然后输入新密码，之后一直y即可
登录mariadb：mysql -u root -p (输入刚才设定的密码即可)
![](/images/swoole161.png)

**2.3 安装phpmyadmin远程管理数据库**
安装phpmyadmin：yum install phomyadmin
由于默认情况下CentOS7上的phpmyadmin只允许从本地访问，因此我们需要修改phpmyadmin的配置
phpMyAdmin默认配置目录为<font color='red'>/etc/httpd/conf.d/phpMyAdmin.conf</font>
然后将限制ip访问的改成允许(共有4条语句要注释，增加两条Require all granted语句)，如下
![](/images/swoole162.png)
详情可点击[这里](http://ask.xmodulo.com/install-phpmyadmin-centos.html)查看
之后输入IP地址/phpmyadmin即可远程登录数据库管理
![](/images/swoole163.png)
在开发环境想不用频繁登录phpmyadmin可以在<font color='red'>/etc/phpMyAdmin/config.inc.php</font>中修改登录模式，有自动登录模式，详情可查看[这里](http://www.jb51.net/article/50028.htm)

---
### 3.客户端实现
客户端代码就十分简易，功能大概就是检验是否连接、设置昵称和发送消息
{% codeblock %}
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8">
	<title>IM聊天室</title>
</head>
<body>
	<div id="msg"></div>
	<input type="text" id="text">
	<input type="submit" value="发送数据" onclick="send()">
</body>
<script>
    var msg = document.getElementById("msg");
    var connectIp = 'ws://39.108.210.229:9501';
    //调用websocket对象建立连接：
    //参数：ws/wss(加密)：//ip:port （字符串）
    var websocket = new WebSocket(connectIp);
    //onopen监听连接打开
    websocket.onopen = function (evt) {
        //websocket.readyState 属性：
        /*
        CONNECTING  0   The connection is not yet open.
        OPEN    1   The connection is open and ready to communicate.
        CLOSING 2   The connection is in the process of closing.
        CLOSED  3   The connection is closed or couldn't be opened.
        */
        if(websocket.readyState == 1)
        {
            msg.innerHTML="连接成功!<br>可使用'#name#+您的昵称'设置昵称<br>";
        }
        //msg.innerHTML = websocket.readyState;
    };

    function send(){
        var text = document.getElementById('text').value;
        document.getElementById('text').value = '';
        //向服务器发送数据
        websocket.send(text);
    }
      //监听连接关闭
//    websocket.onclose = function (evt) {
//        console.log("Disconnected");
//    };

    //onmessage 监听服务器数据推送
    websocket.onmessage = function (evt) {
        msg.innerHTML += evt.data +'<br>';
//        console.log('Retrieved data from server: ' + evt.data);
    };
//监听连接错误信息
//    websocket.onerror = function (evt, e) {
//        console.log('Error occured: ' + evt.data);
//    };

</script>

</body>
</html>
{% endcodeblock %}
上传到服务器端的网页目录[/var/www/html]，之后运行服务器端代码，之后随意用任意浏览器访问客户端页面[ip/xxx.html]即可，具体效果如下，运行服务器脚本后当浏览器两个请求访问时
![](/images/swoole164.png)
可以看到两个用户加入了，然后我们可以设置昵称开始发送消息，每个客户端发送的消息到服务器端都会被服务器端推送至每个客户端
![](/images/swoole165.png)