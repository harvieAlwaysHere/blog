---
title: 使用Hexo和Markdown写作
tags:
  - Hexo
  - GithubPages
date: 2018-01-27 18:03:33
categories: ItEassy
---
![](/images/write.jpg)
### 终于开始激动人心的写博客环节了，但是在hexo上写博客跟word上写博客可不太一样。
1. Hexo特性：hexo可以用命令行创建文章，hexo可以自定义写作布局layout，hexo的资源文件的引用
2. Markdown语法：hexo生成的文章都是.md格式，需要使用特殊的编辑器来进行写作，还有特殊的markdown写作语法
3. 资源的引用：使用外链（图床）和本地引用方法
4. 严格规范markdown语法
### 看来写博客也是一件不省心的事情，就让我们按部就班一步步来看看各个环节的特性吧。
***
### 1.Hexo特性
建议参考[官方文档](https://hexo.io/zh-cn/docs/writing.html)
&emsp;
##### &emsp;&emsp;&emsp;&emsp;1.1 修改layout布局：
&emsp;&emsp;&emsp;&emsp;打开博客文件夹根目录下的[scaffolds]中的[post.md]文件，可以看到初始只有title/date/tags三种文件变量称作Front-matter,可以添加updated/categories等基本变量如下
![](/images/layout.png)
&emsp;&emsp;&emsp;&emsp;Tips:并列的tips需要使用- (-和空格)否则会编译出错
##### &emsp;&emsp;&emsp;&emsp;1.2 创建文章语法：
{% codeblock %}
$ hexo new [layout] <title>
{% endcodeblock %}
##### &emsp;&emsp;&emsp;&emsp;1.3 Hexo自带语法：
&emsp;&emsp;&emsp;&emsp;Hexo也有一些用于写作的不同于md的基本语法称作标签插件(Tag Plugins)
比如**引用块**
{% blockquote 茨威格,断头王后 %}
真是不知命运所馈赠的礼物，早已在暗中标明了价格。
{% endblockquote %}
比如**代码块**
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}


### 2.Markdown语法
&emsp;&emsp;&emsp;&emsp;Markdown语法很多很细致但是很简单，网上有很多文章介绍，这里我就推荐一个写的很好的[博主文章](https://www.jianshu.com/p/0130ad32a08d)供大家学习，相信很多人都是看这篇文章入门的，我就不露丑介绍了。

### 3.资源引用
&emsp;&emsp;&emsp;&emsp;相信很多人在写博文为了增强用户体验都会插入一些图片表达，md中都是依靠链接来插入图片的，唯一不同的是这个链接是本地链接还是外部链接呢，本地链接无风险但是**插入的图片会占据github空间而且不灵活不好分享**毕竟我们写的博文都是需要部署到github上的，外部链接节省github空间但是万一**失效了图片就显示不出来了**俗称图片挂了，我**个人原则是**
>**小图片用本地链接大图片可以尝试外部链接，重要图片用本地链接稍微不那么关键的图片用外部链接。**

##### &emsp;&emsp;&emsp;&emsp;3.1 本地链接的使用：
&emsp;&emsp;&emsp;&emsp;本地链接的使用其实很简单，但是由于图片在本地路径和在生成($ hexo g)文件的路径并不相同，所以我的做法是首先将图片放在**跟博文文件一个目录下**，在本地预览完成需要部署的时候，再将图片剪切到**[博客根目录/source/images/]文件夹**里面同时要**修改博文中对于图片的引用变成(/images/picture.png)**因为编译后图片放在网站根目录的images中。

##### &emsp;&emsp;&emsp;&emsp;3.2 外部链接的使用：
&emsp;&emsp;&emsp;&emsp;外部链接的使用其实就是利用一些网站服务器的空间来存储你的图片俗称**图床**，在博文中直接使用直接路径调用图片，如类似这种链接[https://i.loli.net/2018/01/25/5a69a60eeda77.gif](https://i.loli.net/2018/01/25/5a69a60eeda77.gif)
&emsp;&emsp;&emsp;&emsp;图床有许多，有免费的也有收费的，有些有浏览流量的限制，收费的我推荐[七牛云](https://www.qiniu.com/),免费的我推荐[SM.MS](https://sm.ms/),用到现在还没有出现挂图的迹象。
&emsp;&emsp;&emsp;&emsp;Tips：其实图床蛮多托的，有人建议直接放在github仓库上用绝对连接访问就好。
>在repository的根目录下建立文件夹，然后将图片放在其中，在写链接的时候直接使用自己的GitHub Pages根路径加图片地址访问即可。


### 4.严格规范markdown语法
&emsp;&emsp;&emsp;&emsp;Markdown语法看似简单，其实有许多坑需要踩。
比如最简单的用[###]表示字体大小，也许在markdown编辑器上可以直接使用[###你好]表示，但是生成页面的时候却显示不出来，这是因为[#]的严格语法是**[#+空格+内容]**，缺少一个空格将会造成无法编译。
还有一些**小技巧**，比如想打出空格,换行，底色等，最好还是用字符或者Html语法的形式，如下
{% codeblock %}
&emsp;                                            //空格
<br>                                              //换行
<font face="微软雅黑"></font>                      //字号
<font color=red></font>                           //字体颜色
<font color=#0099ff size=7></font>                //字体[1-7]
<table><tr><td bgcolor=orange></td></tr></table>  //背景色
---                                               //分割线
{% endcodeblock %}

---    
### 相信你已经初步了解怎么使用hexo和markdown语法写作了，你肯定迫不及待地想写下自己第一篇博文打了吧，去吧！
![](/images/go.gif)


