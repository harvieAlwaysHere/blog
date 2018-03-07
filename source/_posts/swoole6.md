---
title: Swoole(六)设置定时器
tags:
  - php项目
  - swoole
date: 2018-02-18 16:26:36
categories: ItEassy
---
### swoole提供了类似JavaScript的setInterval/setTimeout异步高精度定时器，粒度为毫秒级。使用也非常简单。
![](/images/swoole.jpg)
---
### 1.swoole_timer_tick
相当于setInterval，是**持续触发**的
函数原型：int $timer_id swoole_timer_tick(int $after_time_ms, mixed $callback function, mixed $user_param)
参数：
1. int **$after_time_ms**：指定触发的时间，单位为**毫秒**
2. mixed **$callback function**：执行的函数，**参数有$timer_id**
3. mixed **$user_param**：用户参数
返回值：int $timer_id，表示**定时器ID**
---
### 2.swoole_timer_after
相当于setTimeout，仅**在约定的时间触发一次**
函数原型：int $timer_id swoole_timer_after(int $after_time_ms, mixed $callback function, mixed $user_param)
参数：
1. int **$after_time_ms**：指定触发的时间，单位为**毫秒**
2. mixed **$callback function**：执行的函数
3. mixed **$user_param**：用户参数
返回值：int $timer_id，表示**定时器ID**
---

Tips：可以使用swoole_timer_clear(int $timer_id)清除定时器，参数为定时器ID

---
### 3.代码
{% codeblock %}
<?php

//定时器 循环执行
swoole_timer_tick(5000,function($timer_id){
	echo "Tick 5s timer(ID:".$timer_id.") \n";
});

//定时器 单次执行
swoole_timer_after(3000,function($timer_id){
	echo "After 3s timer(ID:".$timer_id.") \n";
});
{% endcodeblock %}
---
### 4.演示
将脚本上传服务器后执行可看到如下结果
![](/images/swoole6.png)