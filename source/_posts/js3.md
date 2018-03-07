---
title: JS（三）HTML DOM对象
tags:
  - JavaScript
  - Web前端
date: 2018-02-26 15:49:29
categories: ItEassy
---
### JS中若要动态改变页面信息和与用户交互，几乎都要使用HTML DOM对象
![](/images/js.jpg)
### HTML DOM对象
![](/images/js14.png)
通过DOM，JS可以创建动态的HTML，如
* 改变页面中所有**HTML元素(改变内容属性等)**
* 改变页面中所有**CSS样式**
* 对页面中所有**HTML DOM事件作出响应**
* 对页面中所有**HTML元素添加删除**

### JS HTML DOM-查找HTML元素
1.使用元素Id<font color='red'>(最常用)</font>：document.getElementById(id)
2.使用标签名TagName：document.getElementsByTagName(tagName)
3.使用类名CalssName：document.getElementsByClassName(className)

### JS HTML DOM-改变HTML
改变HTML输出流：**document.write()**用于直接向HTML输出流写内容
改变HTML内容：通过改变HTML元素的**innerHTML属性**
{% codeblock %}
document.getElementById(id).innerHTML=newHTML;
{% endcodeblock %}
改变HTML属性：通过改变HTML元素的**attribute属性**
{% codeblock %}
document.getElementById(id).attribute=newValue;
{% endcodeblock %}

### JS HTML DOM-改变CSS
改变HTML样式：通过改变HTML元素的**style子属性**
{% codeblock %}
//document.getElementById(id).style.property=newValue;
document.getElementById("p1").style.color="blue";
document.getElementById("p1").style.visibility="hidden"; //元素隐藏
document.getElementById("p1").style.visibility="visible"; //元素显示
改变
{% endcodeblock %}

### JS HTML DOM-事件响应
我们可以在事件发生时执行JavaScript，如用户点击、移动鼠标、提交HTML表单等。
为**HTML事件属性**添加JS代码：**onEvent=JSCode**
**onEvent(HTML事件属性)**：
* **onload/onunload**事件：在用户进入或离开页面时被触发，可用于检测访问者浏览器类型和版本用于加载网页的正确版本，可用于处理cookie
* **onchange**事件：在用户改变输入字段（文本框的文本，选择框的选项等）被触发，可用于对输入字段的验证检测
* **onmouseover/onmouseout**事件：用户鼠标进入或离开HTML元素时触发
* **onmousedown/onmouseup/onclick**事件：用户点击鼠标/释放鼠标/完成点击HTML元素时被触发
* **onfocus**事件：输入字段获得焦点时被触发
更多DOM对象事件可查看[这里](http://www.runoob.com/jsref/dom-obj-event.html)

**JSCode**：可以直接JS代码，也可以是JS函数，参数可传递(this)

### JS HTML DOM-EventListener监听事件
**addEventListener()**用于向指定HTML元素添加事件句柄，新添加的事件句柄不会覆盖已存在的事件句柄，可向一个元素添加多个无论是否是同类型的时间句柄
语法：**element.addEventListener(event, function, useCapture);**
event：事件类型，如"click"/"mousedown"，不使用on前缀
function：事件触发的回调函数
useCapture：布尔值，描述事件是**冒泡还是捕获**，可选参数
![](/images/js15.png)

**removeEventListener()**：用于移除事件的监听
语法：element.removeEventListener(event, function);
event：移除的事件类型
function：移除的回调函数

### JS HTML DOM-添加删除HTML元素
**创建HTML元素**：创建该元素（元素节点），然后向一个已存在的元素追加该元素，如
{% codeblock %}
var element=document.getElementById("div1");  //找到已存在的元素
var para=document.createElement("p");         //创建新元素
var node=document.createTextNode("这是一个新段落。"); //创建文本节点
para.appendChild(node);   //为新元素添加文本节点
element.appendChild(para);  //向已存在的元素添加新元素节点
{% endcodeblock %}
**删除HTML元素**：找到需要删除的元素和它的父元素才能删除该元素，如
{% codeblock %}
var child=document.getElementById("p1");  //找到需要删除的元素
var parent=document.getElementById("div1");    //找到需要删除的元素的父元素
parent.removeChild(child);     //删除元素
{% endcodeblock %}

