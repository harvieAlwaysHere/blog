---
title: Redis(四)Redis-PHP实战
tags:
  - php项目
  - Redis
date: 2018-03-06 09:22:31
categories: ItEassy
---
### 使用PHP操作Redis进行简单的项目开发
![](/images/redis.jpg)

### 简单的字符串缓存
分别使用**set/hSet**方法将对象用**json_encode解析成json字符串**以String/Hash的数据类型存储在Redis缓存中，并用**get/hGet取出数据**，用**json_decode解码**后var_dump输出对象
{% codeblock %}
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strCacheKey = 'stringCacheTest';

//SET应用 存储String类型数据
//所缓存的对象
$arrCacheData = [
    'name' => 'harvie',
    'sex'  => 'male',
    'age'  => '22'
];
//将对象解析成json字符串存储
$redis->set($strCacheKey, json_encode($arrCacheData)); 
//设置缓存有效期 30S过期失效
$redis->expire($strCacheKey, 30); 
//从缓存中取出数据 
$json_data = $redis->get($strCacheKey);
//将取出的json字符串解码成对象形式
$data = json_decode($json_data);
var_dump($data);

//HSET应用 存储Hash类型数据
$hashCacheKey = 'hashCacheTest';
//所缓存的由数组组成的对象
$arrWebSite = [
    'google' => [
        'google.com',
        'google.com.hk'
    ],
];
//添加一个Value到Hash中
$redis->hSet($hashCacheKey, 'google', json_encode($arrWebSite));
$json_data = $redis->hGet($hashCacheKey, 'google');
$data = json_decode($json_data);
var_dump($data);
{% endcodeblock %}
在cli运行后输出为
![](/images/redis40.png)

### 简单队列
**使用lpush入队，使用lrange查看队列数据，使用rpop出队**
{% codeblock %}
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strQueueName  = 'queueName';

//进队列
$redis->lpush($strQueueName, json_encode(['uid' => 1,'name' => 'harvie']));
$redis->lpush($strQueueName, json_encode(['uid' => 2,'name' => 'ryan']));
$redis->lpush($strQueueName, json_encode(['uid' => 3,'name' => 'henry']));
echo "---- 进队列成功 ---- <br /><br />";

//查看队列
$strCount = $redis->lrange($strQueueName, 0, -1);
echo "当前队列数据为： <br />";
print_r($strCount);

//出队列
$redis->rpop($strQueueName);
echo "<br /><br /> ---- 出队列成功 ---- <br /><br />";

//查看队列
$strCount = $redis->lrange($strQueueName, 0, -1);
echo "当前队列数据为： <br />";
print_r($strCount);
{% endcodeblock %}
由于linux对中文适配不好，我采用web远程访问，输入如下
![](/images/redis41.png)

### 订阅发布系统
pub.php中使用**publish推送给订阅的客户端消息**
{% codeblock %}
<?php

ini_set('default_socket_timeout', -1);  //不超时
$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strChannel = 'HarvieChannel';
//发布 推送给订阅的客户端
$redis->publish($strChannel,"From ".$strChannel." Channel Msg!");
echo "---- {$strChannel} ---- Channel Msg Push Success ! <br/>";
$redis->close();
{% endcodeblock %}
sub.php中使用**subscribe方法等待订阅频道的推送**
{% codeblock %}
<?php

ini_set('default_socket_timeout', -1); //不超时
$redis = new Redis();
$redis->connect('39.108.210.229',6379);


$strChannel = 'HarvieChannel';

echo "Wait {$strChannel} Msg Push......";
//订阅 使用subscribe方法接受订阅频道的消息
$redis->subscribe([$strChannel], 'callBackFun');
function callBackFun($redis, $channel, $msg)
{
    print_r([
        'redis'   => $redis,
        'channel' => $channel,
        'msg'     => $msg
    ]);
}
{% endcodeblock %}
运行sub.php客户端，等待消息推送
![](/images/redis42.png)
另外再打开一个终端，运行pub.php服务端推送消息
![](/images/redis43.png)
此时可以看到sub.php终端中可以接收到服务器推送的消息
![](/images/redis44.png)

### 计数器
使用**INCR方法对指定的Key的Value+1**
{% codeblock %}
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strKey = 'commentsCount';

//设置初始值
$redis->set($strKey, 0);
//使用INCR方法指定Key增加Value
$redis->INCR($strKey);  //+1
$redis->INCR($strKey);  //+1
$redis->INCR($strKey);  //+1

$strNowCount = $redis->get($strKey);

echo "Now the num is {$strNowCount}";
{% endcodeblock %}
![](/images/redis45.png)

### 排行榜
使用**zSet有序集合数据类型**实现排行榜的自动排序，**ZREVRANGE/ZRANGE方法**取特定范围内**从大到小/从小到大**排序，可指定是否带分数
{% codeblock %}
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strKey = 'rankTest';

//zadd向zSet集合添加数据
$redis->zadd($strKey, '50', json_encode(['name' => 'Tom']));
$redis->zadd($strKey, '70', json_encode(['name' => 'John']));
$redis->zadd($strKey, '90', json_encode(['name' => 'Jerry']));
$redis->zadd($strKey, '30', json_encode(['name' => 'Job']));
$redis->zadd($strKey, '100', json_encode(['name' => 'LiMing']));

//ZREVRANGE取特定范围内从大到小排序的数组，最后一个参数为是否带分数显示
$dataOne = $redis->ZREVRANGE($strKey, 0, -1, true);
echo "---- {$strKey}由大到小的排序 ---- <br /><br />";
print_r($dataOne);

//ZRANGE取特定范围内从小到大排序的数组，最后一个参数为是否带分数显示
$dataTwo = $redis->ZRANGE($strKey, 0, -1, true);
echo "<br /><br />---- {$strKey}由小到大的排序 ---- <br /><br />";
print_r($dataTwo);
{% endcodeblock %}
![](/images/redis46.png)

### 字符串悲观锁
确保在**多个事务**同时存取数据库中**同一数据**时不破坏事务的隔离性和统一性，以及数据库的统一性，**乐观锁和悲观锁是并发控制主要采用的技术手段**

悲观锁：假定**会发生并发冲突**，屏蔽一切可能违反数据完整性的操作
* 实现机制：查询完数据的时候就把**事务锁起来**，直到**提交事务**
* 实现方式：数据库中的锁机制
* 实现特点：适合**强一致**场景，**效率较低**，特别是并发读的效率低

**PHP+Redis实现悲观锁**：利用redis中的**setnx方法的原子性操作**，设置一个LockKey，加锁的实质就是**向redis中添加一个这个LockKey和过期时间**，每次多个并发事务需要访问数据时，均先**获取锁(就是用setnx方法设置LockKey字段)**，只有首先设置了字段的事务才会**得到锁(就是返回Ture)**，进行接下来的数据处理操作，没有设置字段的事务就会**阻塞直到锁过期或者获取锁的事务释放锁(就是删除LockKey字段)**
{% codeblock %}
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

/**
 * 获取锁
 * @param  String  $key    锁标识
 * @param  Int     $expire 锁过期时间
 * @return Boolean
 */

function lock($key = '', $expire = 5) {
	//php函数无法调用外部变量
	global $redis;
	//获取锁
    $isLock = $redis->setnx($key, time()+$expire);
    //不能获取锁
    if(!$isLock){
        //判断锁是否过期
        $lockTime =$redis->get($key);
        //锁已过期，删除锁，重新获取
        if (time() > $lockTime) {
            unlock($key);
            $isLock = $redis->setnx($key, time() + $expire);
        }
    }

    return $isLock? true : false;
}

/**
 * 释放锁
 * @param  String  $key 锁标识
 * @return Boolean
 */
function unlock($key = ''){
	//php函数无法调用外部变量
	global $redis;
    return $redis->del($key);
}

// 定义锁标识
$key = 'PessimisticLock';

// 获取锁
$isLock = lock($key, 10);
while(!$isLock){
	//获取锁失败 等待锁释放或者超时再获取
	echo 'wait..';
	sleep(1);
    $isLock = lock($key, 10);
}
//获取锁成功开始执行事务
echo 'get lock success<br>';
echo 'do sth..<br>';
sleep(5);
echo 'success<br>';
unlock($key);
{% endcodeblock %}

**模拟测试**：我们打开两个终端模拟并发过程，一个终端先获取锁执行事务，另一个终端获取锁失败之后每隔1s再次获取，直到第一个终端执行完事务(5s)释放锁或者锁超时(10s),这个实例里面是执行完事务就释放锁，另一个终端才开始获取到锁执行事务
第一个终端获取锁开始执行事务
![](/images/redis49.png)
第二个终端获取锁失败开始等待
![](/images/redis47.png)
第一个终端执行完成事务释放锁
![](/images/redis48.png)
第二个终端获取锁开始执行事务最后完成
![](/images/redis410.png)

### 字符串乐观锁
乐观锁：假定**不会发生并发冲突**，只在**提交操作**时检查是否违反数据完整性
* 实现机制：在**修改数据**时将事务锁起来，通过**version方式进行锁定**
* 实现方式：使用**version版本或者时间戳**
* 实现特点：适合**多读写少**，**并发冲突少**的场景

**PHP+Redis实现乐观锁**：利用redis中的**watch($Key)方法(watch可以监视一个KEY在事务提交exec()时是否改变，若改变则exec()会失败)**，事务使用**multi()开启和exec()批量提交**

{% codeblock %}
//optimisticLock.php 
<?php

$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strKey = 'optimisticLcok'; //类似Mysql中的version

//设置其version
$redis->set($strKey,10);
//watch监视Key
$redis->watch($strKey);

$versionNum = $redis->get($strKey);
echo "---- First Version:{$versionNum} ---- <br/><br/>";

//multi()开启事务
$redis->multi(); 

$redis->set('ID',5);
sleep(5);
//第一个事务执行完准备提交时会发现版本号变了 提交失败
$redis->exec();

//查看此时版本号 
$versionNum = $redis->get($strKey);
echo "---- Current Version:{$versionNum} ---- <br/><br/>"; 
//查看执行的事务设置ID是否回滚
$id = $redis->get('ID');
echo "Set ID is :".$id."<br>";
//当exec时候如果监视的key从调用watch后发生过变化，则整个事务会失败
{% endcodeblock %}
{% codeblock %}

<?php
//changeVersion.php 模拟并发程序
$redis = new Redis();
$redis->connect('39.108.210.229',6379);

$strKey = 'optimisticLcok'; 

//设置其version
$redis->set($strKey,11);
{% endcodeblock %}
先试试**无并发情况**直接执行
![](/images/redis411.png)
可以看到版本号没改变而且事务执行设置ID成功，我们先删除所有键
![](/images/redis412.png)
可以看到ID键已经清空了，然后试试**有并发情况**，执行事务时另一个并发程序改变了乐观锁(Version)的值
![](/images/redis413.png)
可以看到若有并发程序改变了版本号，则事务并不能执行成功，ID键值设置失败了
