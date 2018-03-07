---
title: 使用github pages和hexo搭建个人博客
date: 2018-01-14 20:51:14
tags: 
- Hexo
- GithubPages
categories: ItEassy
---
![](https://i.loli.net/2018/01/25/5a69ac586ce86.jpg)
### 大致步骤可以分为6步
1. 下载安装Node.js（可以使用npm下载和管理包）
2. 申请github账户（用于注册github pages仓库）
3. 下载git（最好是bash 便于使用命令行操作系统）
4. 安装hexo并配置基本参数
5. 安装hexo主题并配置基本参数
6. 博客部署到github pages上线
***

### 1.  下载安装Node.js（可以使用npm下载和管理包）
1.1访问[Node.js中文官网](http://nodejs.cn/download/)下载安装包:
![](https://i.loli.net/2018/01/25/5a696446c21d0.png)
根据你的操作系统和系统类型（位数）选择下载哪一个安装包
Tips：32位安装包兼容64位操作系统，64位安装包不兼容32位操作系统
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;建议使用.msi格式一键安装
&nbsp;
&nbsp;
1.2安装Node.js:
&emsp;&emsp;&emsp;&emsp;.zip文件：运行node.exe文件会跳出命令行安装
&emsp;&emsp;&emsp;&emsp;.msi文件:根据提示步骤安装（安装路径最好是全英文）
1.3测试是否安装成功:
&emsp;&emsp;&emsp;&emsp;1.3.1 打开命令提示符:
&emsp;&emsp;&emsp;&emsp;windows系统下用![](https://i.loli.net/2018/01/25/5a69690239d22.png)+R）打开运行窗口输入cmd打开命令提示符
&emsp;&emsp;&emsp;&emsp;1.3.2： 输入命令 node -v 和npm -v 查看node.js和npm是否安装成功：
&emsp;&emsp;&emsp;&emsp; ![](https://i.loli.net/2018/01/25/5a6969e9efb8f.png)
&emsp;&emsp;&emsp;&emsp; 若有版本号（不一定与我的相同）则安装成功 否则安装失败则重新安装即可
***


### 2. 申请github账户（用于注册github pages仓库）
&emsp;&emsp;&emsp;&emsp;2.1 访问[github官网](https://github.com)注册账号
&emsp;&emsp;&emsp;&emsp;2.3 申请一个仓库:
&emsp;&emsp;&emsp;&emsp;登录你的github账号点击上方的New repository 如下图所示
![](https://i.loli.net/2018/01/25/5a696b692e2fc.png)
&emsp;&emsp;&emsp;&emsp;在Repository name上填写 [你注册的github名称.github.io] 即可
&emsp;&emsp;&emsp;&emsp;如我注册的github名称是xxx则我的Repository name为 [xxx.github.io]
&emsp;&emsp;&emsp;&emsp;其他的都不用填写 之后点击[Create repository]即可
***

### 3. 下载安装git（最好是bash 便于使用命令行操作系统）
>Git是一款免费、开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

此部分内容建议参考[廖雪峰老师的git安装文档](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137396287703354d8c6c01c904c7d9ff056ae23da865a000)
&emsp;&emsp;&emsp;&emsp;3.1 通俗来说git就是一个软件类似于cmd命令行，但是它可以使用git命令和linux命令来操控系统，这是cmd所做不到的。在此项目中，我们主要使用它来编写npm命令、hexo命令、git命令来生成和部署自己的博客到github pages上
&emsp;&emsp;&emsp;&emsp;访问[git下载页面](https://git-scm.com/downloads)下载对应系统的git

&emsp;&emsp;&emsp;&emsp;安装完毕之后打开gitbash即可使用
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a698287b9b84.png)
&emsp;&emsp;&emsp;&emsp;首次使用git的用户还需配置自己的用户名和地址用于验证身份：
{% codeblock %}
$ git config --global user.name "Your Name" 
$ git config --global user.email "email@example.com" 
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;3.2 将计算机的ssh key设置到你的github账号上，你才能将本地的文件上传到你的github上面
&emsp;&emsp;&emsp;&emsp;具体操作可参考[廖雪峰老师的ssh key设置教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

***

### 4. 全局安装hexo建立自己的博客并配置基本参数
此部分内容建议参考[hexo官方文档](https://hexo.io/zh-cn/docs/index.html)
&emsp;&emsp;&emsp;&emsp; 4.1 全局安装hexo：
&emsp;&emsp;&emsp;&emsp;访问[hexo官方网站](https://hexo.io/zh-cn/)查看安装命令，运行Git Bash输入安装命令npm install hexo-cli -g
&emsp;&emsp;&emsp;&emsp; 4.2 建立自己的博客：在Git Bash中输入 
{% codeblock %}
$ hexo init [你的博客文件夹名称] 
$ cd [你的博客文件夹名称] 
$ npm install
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;我这里演示的文件夹名称是[testBlog]
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a69886d4d1c7.png)
&emsp;&emsp;&emsp;&emsp;Tips:输入pwd可查看当前文件夹的路径
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp;输入explorer .可打开命令行当前文件夹
&emsp;&emsp;&emsp;&emsp;如果看到以下内容，那么恭喜你hexo建站成功了
&emsp;&emsp;&emsp;&emsp; ![](https://i.loli.net/2018/01/25/5a698a85db6e0.png)
&emsp;&emsp;&emsp;&emsp; 4.3 配置基本参数博客本地预览
&emsp;&emsp;&emsp;&emsp;您可以在 _config.yml 文件中修改大部份的配置具体参数意义可参考[官方文档](https://hexo.io/zh-cn/docs/configuration.html)
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a698b5ba81c3.png)
&emsp;&emsp;&emsp;&emsp;输入以下命令可在本地预览你生成的博客网址
{% codeblock %}
$ npm install hexo-server --save 
$ hexo generate
$ hexo server 
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a698d29e35bd.png)
&emsp;&emsp;&emsp;&emsp;你可以像我一样用[$ hexo g]和[$ hexo s]来简化命令
&emsp;&emsp;&emsp;&emsp;[$ npm install hexo-server --save]用来安装hexo本地预览的服务器 只用第一次执行一次就可以了
&emsp;&emsp;&emsp;&emsp;以后每次改动就只用[$ hexo g]生成文件和[$ hexo s]启动服务器就可以了
&emsp;&emsp;&emsp;&emsp;之后在浏览器输入[http://localhost:4000]就可以查看你的博客页面了
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a698e9da8343.png)
***

### 5. 安装hexo主题并配置基本参数
&emsp;&emsp;&emsp;&emsp;到目前为止，是不是觉得hexo默认的主题不符合你的心意，没关系，hexo爱好者们提供了上千个主题供你挑选使用，安装主题的方法也十分简单，让我们一起来让博客的面目焕然一新吧
&emsp;&emsp;&emsp;&emsp;5.1 挑选你喜爱的主题下载
&emsp;&emsp;&emsp;&emsp;可以参考[官方主题推荐文档](https://hexo.io/themes/)或者[github大神整理](https://github.com/hexojs/hexo/wiki/Themes)来寻找自己喜欢的主题并按照对应主题的文档说明下载使用
&emsp;&emsp;&emsp;&emsp;如[我的博客](https://harviealwayshere.github.io/)采用的主题是[Huemen](https://github.com/ppoffice/hexo-theme-hueman)下载就十分简单，按照官方文档按部就班即可
&emsp;&emsp;&emsp;&emsp;![](https://i.loli.net/2018/01/25/5a699295d3479.png)
&emsp;&emsp;&emsp;&emsp;5.2 配置主题的基本参数：
&emsp;&emsp;&emsp;&emsp;主题的配置是类似于hexo配置，修改主题文件下中的[_config.yml]文件即可具体每个参数的意义请参考主题的说明文档
![](https://i.loli.net/2018/01/25/5a6996d20c594.png)
&emsp;&emsp;&emsp;&emsp;5.3 配置成功之后可运行命令预览自己的博客了（在博客根目录下运行命令）
{% codeblock %}
$ hexo clean 
$ hexo g 
$ hexo s 
{% endcodeblock %}
![](https://i.loli.net/2018/01/25/5a6998588778a.png)
***


### 6. 博客部署到github pages上线
&emsp;&emsp;&emsp;&emsp;6.1 安装git pages部署器：在博客根目录下打开git bash运行[$ npm install hexo-deployer-git --save]命令
&emsp;&emsp;&emsp;&emsp;6.2 修改博客根目录下的配置文件[_config.yml]中的deploy参数：
{% codeblock %}
deploy: 
   type: git 
   repo: [你注册的github pages的仓库地址] 
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;6.3 部署至github上： 
{% codeblock %}
$hexo generate 
$hexo deploy 
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;或者
{% codeblock %}
$hexo d -g
{% endcodeblock %}
&emsp;&emsp;&emsp;&emsp;成功之后你可以在线访问你的github pages地址[你的github仓库名.github.io]来查看你的博客了
***


## 恭喜你，你的博客已经搭建成功并上线了!
![](https://i.loli.net/2018/01/25/5a69a4a4cdfa8.gif)
### 之后我会再写一篇文章介绍hexo写作的基本方法、使用markdown格式写作、图床的使用技巧等博文写作知识，敬请期待。
![](https://i.loli.net/2018/01/25/5a69a60eeda77.gif)

