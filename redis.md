# redis入门

## 一、简介

> Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用；
- Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储；
- Redis把数据存在内存中，并在磁盘中记录数据的变化，因为将数据存在内存中，所以数据操作非常快；

## 二、安装启动

1. 三平台安装[安装教程](https://www.runoob.com/redis/redis-install.html)
2. 安装目录下运行
```bash
redis-server
```

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20200112191437.png)

## 三、数据操作

1. 字符串（String）

```bash
set name zxc

get name

```

2. 哈希值（Hash）

```bash
hmset myhash f1 "hello" f2 "world"

hget myhash f1

```
3. 列表（List）

```bash
lpush mylist "world"
lpush mylist "hello"

lrange mylist 0 -1
```

4. 集合（Set）
redis的set是string类型的无序集合。集合成员是唯一的，这就意味着集合不能有重复数据
```bash
sadd myset "hello"
sadd myset "world"
sadd myset "hello"

smembers myset
```

5. 有序集合（zset）
在redis中，有序集合合集合都是String类型的元素集合，且不允许出现重复元素。二者不同的是，有序集合的每个元素都会关联一个Double类型的数值。Redis正是通过此数值为集合中的成员进行从小到大的排序，有序集合的成员是唯一的，但是数值是可以重复的

```bash
szset 1 "one"
szset 3 "three"
szset 2 "two"
zrange myzset 0 -1
zrange myzset 0 -1 withscores
```

## 四、node中使用redis

1. 启动redis服务
2. 安装node_redis包
```bash
npm install redis --save
```
3. 编写代码初始化
```js
const redis = require('redis')
const client = redis.createClient(6379,'127.0.0.1')
client.on('error', function(){
    console.log('Error:' + err)
})
```
4. node中操作数据库
```js
// 操作k-v
client.set('name','zxc')
client.get('name', function(){})
// 操作hash
client.hmset('zxc' ,{
    'age': '28',
    'sex': '1'
})
client.hgetall('zxc', function(){})
// 操作list
client.lpush('zxc', 'koa', redis.print)
client.lrange('zxc', 0, -1, function(){})
// 操作Set
client.sadd('address', '北京', redis.print)
client.sadd('address', '上海', redis.print)
client.smembers('address', function(){})
```

## 五、持久化用户Session状态
> http是一种无状态的协议，本身无法标士一次特定的绘画，所以需要一种机制去保存当前用户的特定信息，session就是其中一种保存会话信息的实现方式。因为session中的数据一般都是短时间内高频访问的，需要保证性能，所以较好的方式是内存配合Redis做一个持久化

> 除了在服务器端保存完整的session数据外，在客户端浏览器通过cookie来保存session的一个唯一标识，通常是一个id，在http的请求头中带上cookie信息，并可以根据情况使用HttpOnly为true，防止恶意篡改session的id

![](https://raw.githubusercontent.com/zxcweb/pic-go/master/img/20200112211406.png)

