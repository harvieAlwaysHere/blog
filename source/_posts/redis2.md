---
title: Redis(二)客户端操作(cli)
tags:
  - php项目
  - Redis
date: 2018-03-04 15:48:40
categories: ItEassy
---
### 在命令行通过客户端操作Redis
![](/images/redis.jpg)

### 客户端常用命令
ping: 查看服务器是否允许
quit: 关闭当前连接
auth: 验证密码
select: 选择数据库，0-15，共16个，默认使用0
flushdb: 删除当前数据库
flushall: 删除所有数据库
del: 删除键
exists: 检查键是否存在

ping/auth测试
![](/images/redis20.png)
set/get/select/flushdb/flushall测试
![](/images/redis21.png)
del/exists测试
![](/images/redis22.png)

### 数据类型
Redis不仅仅支持简单的**key-value类型的数据**，同时还提供**list，set，zset，hash**等数据结构的存储

<font color='##00008b'>**1.字符串(String)**</font>：最基本的数据类型，**Key-Value结构**
* 设置：set key value
* 获取：get key

<font color='##00008b'>**2.哈希(Hash)**</font>：**键值对的集合**，适合**存储对象**
* 设置：hmset objectName key1 value2 key2 value 2...
* 获取单个对象所有信息：hgetall objectName
* 获取单个对象单个信息：hget objectName key
![](/images/redis23.png)

<font color='##00008b'>**3.列表(List)**</font>：按照插入顺序排序，可以添加元素到列表头部（左边）或尾部（右边）
* 左侧压入数据：lpush key value1 value2...
* 左侧弹出数据：lpop
* 右侧压入数据：rpush key value1 value2...
* 右侧弹出数据：rpop
* 范围显示：lrange key start stop
* 显示个数：llen key
![](/images/redis24.png)

<font color='##00008b'>**4.集合(Set)**</font>：string类型的无序集合，集合中**元素时唯一的**
* 添加数据：sadd key value1 value2...
* 显示数据：smembers key
* 显示总数：scard key
* 随机移除：spop key [count]
![](/images/redis25.png)


<font color='##00008b'>**5.有序集合(Zset:SortSet)**</font>：每个元素都需要**关联一个double类型的分数**，redis根据分数为集合中的成员进行**排序**
* 添加数据：zadd key score value
* 显示数据：zrange key start stop
* 显示总数：zcard key
* 显示范围：zcount key start stop
* 显示序号：zrank key member
* 显示分数：zscore key member
![](/images/redis26.png)