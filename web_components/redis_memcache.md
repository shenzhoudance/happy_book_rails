#Redis

Redis一个键值储存系统，也就是我们说得NOSQL数据库。

##优点
数据保存在硬盘上，读取快；占用更小的内存；当然还有和其他数据库一样的特性，像集群，数据库复制等。

##安装
```shell
$ wGET http://download.redis.io/releases/redis-3.0.4.tar.gz
$ tar xzf redis-3.0.4.tar.gz
$ cd redis-3.0.4
$ make
```
##启动服务
```shell
$ src/redis-server
```

##运行
```shell
$ src/redis-cli
redis> SET foo bar
OK
redis> GET foo
"bar"
```

##使用
###基本操作
`SET Key Value #设置键和值`

`SET floor 10`

`GET Key #获得键的值`

`GET floor => 10`

`INCR Value #值自动加一`

`INCR floor => 11`

`DEL Value #删除键`

`DEL floor`

`EXPIRE Key Seconds #设置键在多少秒后自动销毁`

```
SET resource "Demo"
EXPIRE resource 120
```

`TTL Key #显示键在多少秒后销毁`

```
TTL resource => 120
TTL resource => -2 #-2代表键已销毁
```

`TTL Key=> -1 # -1代表这个键没有设置自动销毁`

###List操作

```
RPUSH friends "Lily" #在list尾增加值
RPUSH friends "Lucy"
LPUSH friends "Alan" #在list首增加值

LRANGE friends 0 -1 => 1) "Alan", 2) "Lily", 3) "Lucy" #显示所有值
LRANGE friends 0 1 => 1) "Alan", 2) "Lily"
LRANGE friends 1 2 => 1) "Lily", 2) "Lucy"

LLEN friends => 3 #显示值个数
LPOP friends => "Alan" #移除第一个值
RPOP friends => "Lucy" #移除最后一个值
```
###Set操作
>于List的不同之处在于他没有固定的顺序和他的值可能只出现一次

```
SADD friends "Alan"
SADD friends "Lucy"
SADD friends "Lily"

SMEMBERS friends => 1) "Lily" 2) "Lucy"` 3) "Alan" #显示所有的值

SREM friends "Alan" #在set中删除这个值

SISMEMBER friends "Lucy" => 1 #1代表set中存在该值
SISMEMBER friends "Alan" => 0 #0代表set中不存在该值

SADD enemy "Jack"
SADD enemy "Zed"
SUNION friends enemy => 1) "Jack" 2) "Lucy" 3) "Lily" 4) "Zed"

SADD runner 1009 "Alan"
SADD runner 1000 "Jack"
SADD runner 1003 "Hoan"

ZRANGE runner 1 2 => 1) "Hoan" 2) "Alan" #在Redit1.2中增加了set得排序功能
```
###Hash操作
```
HSET user:1000 name "Jack"
HSET user:1000 address "Beijing chaoyang"

HMSET user:1000 name "Jack" address "Beijing chaoyang" #以上这两种键值设置方法都可以

HGET user:1000 name => "Jack" #得到键的值
HGETALL user:1000 => 1) "name" 2) "Jack" 3) "address" 4) "Beijing chaoyang"

HSET user:1000 visits 100
HINCRBY user:1000 visits 1 => 101 #HASH值的增加
HINCRBY user:1000 visits 100 => 201
HDEL user:1000
```


#缓存服务器：MemCache

&emsp;&emsp; Memcache是一免费开源、性能高、具有分布式对象的缓存系统。目前大都用于大负载网站的数据库分压。其工作机制是在内存中开辟一块空间然后建立HashTable。
##工作原理

&emsp;&emsp; memcached(MemCache在服务器端的主程序文件名)以守护程序方式运行在一或多个服务器中，随时接受来自客户端的请求。

*注：MemCache为项目名称，memcached是MemCache在服务器端的主程序文件名。*

##操作流程

>1. 检查客户端的请求数据是否在memcached中，有数据则把请求数据返回并且不对数据库进行操作；没有则查找数据库并且把从数据库中获取的数据返回给客户端，同时将数据库缓存一份到memcached中。
>2. 为保证数据的一致性，更新数据库的同时更行memcached中的数据。
>3. 如果分配给memcached的内存空间用完后，使用LRU（Least Recently Used,最近最少使用）策略加上到期失效策略，失效数据先被替换然后替换掉最近最少被使用的数据。

##MemCache的安装和启动
###Mac下安装
>**Step1 Homebrew（系统软件管理程序)安装**
>
```
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
也可以参考http://blog.zerosharp.com/installing-ruby-with-homebrew-and-rbenv-on-mac-os-x-mountain-lion/ 下载homebrew

>**Step2 安装memcached**
>
```
$ brew search memcache
```
返回结果：
>
```
libmemcached    memcache-top    memcached   memcacheq
```
则安装服务器端：
>
```
$ brew install memcached
```
>完成后查看安装结果
>
```
>$ which memcached
>$ memcached -h
```
>**Step3 安装libmemcached**
>安装客户端
>```
>$ brew install libmemcached
```
>**Step4 启动服务器**

>默认参数启动：
>
```
$ /usr/local/bin/memcached -d
```
>**Step5 测试**

>以守护程序的形式启动 memcached（-d），为其分配2GB内存（-m 2048），并指定监听 localhost端口 11211
>```
$ ./memcached -d -m 2048 -l 10.0.0.40 -p 11211
```

>使用一个简单的telnet客户机连接到memcached服务器
>
```
>$ telnet localhost 11211
```
>成功则返回 Connected to localhost（已经连接到 localhost）。


##命令行介绍


### 六项存命令

>- Set：添加一个新条目到memcached或是用新的数据替换替换掉已存在的条目。
存入命令格式为:

>```
><command> <key> <flags> <exptime> <bytes>
>
```
例如存入key为yanmin的yanmin.in值：
>
```
$ set yanmin 0 0 9
$ yanmin.in
```

>- Add：当KEY不存在的情况下，它向memcached存数据，否则，返回NOT_STORED响应
>- Replace：当KEY存在的情况下，它才会向memcached存数据，否则返回NOT_STORED响应
>- Cas:改变一个存在的KEY值 ，但它还带了检查的功能
>- Append:在这个值后面插入新值
>- Prepend:在这个值前面插入新值


###两项取命令
>- Get:取单个值 ，从缓存中返回数据时，将在第一行得到KEY的名字，flag的值和返回的value长度，真正的数据在第二行，最后返回END，如KEY不存在，第一行就直接返回END 。
例如：

>```
>$ get yanmin
>```
>返回：

>```
>VALUE yanmin 0 9
yanmin.in
```
>- Get_multi：一次性取多个值

###一项删除命令
>Delete


