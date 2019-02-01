---
title: 初探redis
date: 2018-08-04 17:30:33
tag: [redis]
categories: 后端
---

记录redis学习与使用
<!--more-->

## window安装

在这个地址[下载redis@2.8.2402](https://github.com/MicrosoftArchive/redis/releases/tag/win-2.8.2402)，下载完成后，cmd进入到这个文件夹。

```
# 启动服务端
redis-server.exe
```
![win启动redis服务器](win-run-redis-server.png)

新开一个cmd，启动客户端
```
# 启动客户端
redis-cli.exe

# 存储一个值
set hello world

# 获取一个值
get hello

# 列出所有的内容
keys *
```

![win下redis基本操作](win-redis-operation.png)

## Linux/mac 安装

在这个地址[下载redis@2.8.0](http://download.redis.io/releases/)，解压文件，并进入根目录，执行一下命令

```
$ make
```

![mac下编译redis](mac-build-redis.png)


```
$ sudo make test
```

![运行redis测试](mac-build-test-success.png)


```
$ cd src/

# 启动服务器
$ ./redis-server

# 启动客户端
$ ./redis-cli

```

这样，就可以mac上使用redis了

## 服务端换个端口启动
默认的端口是6379。

第一种方式是通过在命令行指定参数来启动

```
# 使用6390来启动redis服务端
$ ./redis-server --port 6390

# 客户端连接6390服务端
$ ./redis-cli -p 6390

```
第二种是修改配置文件中的port，然后指定配置文件来启动
```
$ ./redis-server ../redis.conf
```

## 如何停止redis

因为都是在命令行上进行操作，所以可以使用`Ctrl + C` 进行停止。但是使用这种方式来停止redis有个问题，那就是redis里面的数据不会被持久化。

那咋办？第一种方式可以通过人为的去保存redis里面的数据，这样即使`Ctrl + C`停止了，数据也保存在磁盘

```
// 在客户端人为触发redis持久化数据
127.0.0.1:6379> save

```

除了上面的方式，最好还是使用下面的命令来停止redis

应该使用一个`shutdown`命令来停止redis服务，因为这个命令会自动把redis的数据自动持久化到磁盘

```
$ ./redis-cli shutdown
```

PS: 如果不是使用默认的端口，停止redis的时候也要指定端口停止，如果还指定了ip，也要用-h参数来指明是哪个ip的redis服务器

```
$ ./redis-cli -p 6390 shutdown
```
## 使用别的redis服务
```
$ ./redis-cli -p 6390 -h 192.168.0.100
```

## 使用密码
```
$ ./redis-cli -p 6390 -a xxx
```

## 常用的命令

+ flushall
+ del , 1代表成功
+ exists
+ ttl，返回值为-1的时候，是没有过期时间的，-2 代表不存在
+ expire userName 10 设置useName的过期时间为10s
+ type useName 返回useNmae这个key的类型
+ rename 重命名
+ renamenx
+ setex
+ psetex
+ getrange
+ getset
+ mset a a1 b b1 c c1 同时设置多个key和value
+ mget a b c 同时获取a，b，c的值
+ setnx a aa 当a不存在的时候才设置key为a，value为aa
+ strlen 字符串的长度
+ msetnx
+ incr a 使a的value加一，a的value要是数值才行
+ decrby
+ append
