---
title: JS（二）HTML 浏览器BOM对象
tags:
  - JavaScript
  - Web前端
date: 2018-02-26 15:49:15
categories: ItEassy
---
### 浏览器对象模型（Browser Object Model (BOM)使 JavaScript 有能力与浏览器"对话"。
![](/images/js.jpg)
所有浏览器都支持**window 对象**，它表示**浏览器窗口**。
**window对象的成员**：所有JavaScript**全局对象、函数以及变量**
**window对象的属性**：全局变量
**window对象的方法**：全局函数
Tips：所有Window对象方法都可以**不使用window前缀**，直接调用全局方法

### Window Screen：包含有关用户屏幕的信息
window.screen对象在编写时**可以不使用window这个前缀**
**screen对象属性**：
1. screen.availWidth：可用的屏幕宽度，以**像素**计，减去界面特性，比如窗口任务栏
2. screen.availHeight：可用的屏幕高度，以像素计，减去界面特性，比如窗口任务栏

### Window Location：包含URL信息
window.location对象用于**获得当前页面的地址(URL)**，并把浏览器**重定向到新的页面**
**location对象属性**：
1. location.href：返回**当前界面的完整URL**，如http://www.w3school.com.cn/js/js_window_location.asp
2. location.hostname：返回**web主机的域名**，
3. location.pathname：返回**URL的路径名**，如/js/js_window_location.asp
4. location.port：返回**web主机的端口**(80或443)
5. location.protocol:返回所使用的**web协议**（http:// 或 https://）

**location对象方法**：
1. location.assign(URL)：加载新的文档，打开URL地址新界面
2. location.reload(boolean)：重新加载当前文档，false则会根据文档是否改变决定是否使用缓存加载，true则无论文档是否改变均会会绕过缓存重新加载文档

### Window History：包含浏览器历史
**history对象方法**：
1. history.back()：加载历史列表中的前一个URL界面
2. history.forward()：加载历史列表中的下一个URL界面

### Window Navigator：包含有关访问者浏览器的信息
由于Navigator信息**可被浏览器使用者更改**或者**浏览器对于测试站点识别错误**，所以信息具有**误导性**，不应该用于检测浏览器版本
**navigator对象方法**：
* navigator.appCodeName：浏览器代号，如Mozilla
* navigator.appName：浏览器名称，如Netscape
* navigator.appVersion：浏览器版本，如Chrome/61.0.3163.100
* navigator.cookieEnabled：是否启动启用Cookies，true
* navigator.platform：硬件平台，如Win32
* navigator.userAgent：用户代理，如Mozilla/5.0
* navigator.systemLanguage：用户代理语言，如undefined

### Window PopupAlert:弹窗
1.**window.alert()**：警告框，**确保用户得到某些消息**，用户需**点击确定**才能继续操作
![](/images/js20.png)
2.**window.confirm("sometext");**：确认框，**验证用户是否接受操作**，用户可以点击**确认或者取消**，相应**返回true/false**
![](/images/js21.png)
3.**window.prompt("sometext","defaultvalue");**：提示框，提示用户**需要输入某些值**，然后点击**确认或者取消**，相应**返回输入的值/null**
![](/images/js22.png)
4.**window.open(URL,name,features,replace)**：打开一个URL新窗口并可以通过features控制其外观或者利用name查找一个已命名窗口
![](/images/js24.png)

### Window Timing：计时
JS中设定一个时间间隔之后来执行代码，我们称之为**计时事件**，主要使用两个关键方法
1.**window.setInterval(callback function,milliseconds);**：在指定milliseconds毫秒数**不断执行**callback function回调函数，返回计时器id，可用**clearInterval(id)**停止计时器
2.**window.setTimeout(callback function,milliseconds);**：在milliseconds毫秒后**执行一次**callback function回调函数，返回计时器id，可用**clearTimeout(id)**在函数执行前停止计时器
Tips：可使用setTimeout回调自身函数来实现无穷循环
Tips：利用setInterval创建一个时钟(每秒钟更新一次时间)
{% codeblock %}
<p id="demo"></p>
<script>
var myVar=setInterval(function(){myTimer()},1000);
function myTimer(){
	var d=new Date();
	var t=d.toLocaleTimeString();
	document.getElementById("demo").innerHTML=t;
}
</script>
{% endcodeblock %}

### Window Cookie：存储Web页面用户信息
cookie是存储于访问者的计算机中的变量，当web服务器向浏览器发送web页面时，在连接关闭后，服务端不会记录用户的信息，可以利用Cookie记录客户端的用户信息
用户信息在Cookie中以**键值对的形式存储**，如name=key，多个信息可用**;分割**
1.创建Cookie：**document.cookie="username=harvie;expires="+Date();**
2.读取Cookie：**var x = document.cookie;**，以**字符串形式返回所有cookie**
3.删除Cookie：即使Cookie过期，即设置expires属性为以前的时间，如**expires=Thu, 01 Jan 1970 00:00:00 GMT**
Tips：我们使用函数设置、获取、检测Cookie
{% codeblock %}
//设置Cookie（name+exdays）
function setCookie(cname,cvalue,exdays){
    var d = new Date();
    d.setTime(d.getTime()+(exdays*24*60*60*1000));
    var expires = "expires="+d.toGMTString();
    document.cookie = cname+"="+cvalue+"; "+expires;
}
//获取Cookie
function getCookie(cname){
    var name = cname + "=";
    var ca = document.cookie.split(';');
    for(var i=0; i<ca.length; i++) {
        var c = ca[i].trim();
        if (c.indexOf(name)==0) { return c.substring(name.length,c.length); }
    }
    return "";
}
//检测Cookie(可在页面载入onload时执行checkCookie函数)
function checkCookie(){
    var user=getCookie("username");
    if (user!=""){
        alert("欢迎 " + user + " 再次访问");
    }
    else {
        user = prompt("请输入你的名字:","");
          if (user!="" && user!=null){
            setCookie("username",user,30);
        }
    }
}
{% endcodeblock %}