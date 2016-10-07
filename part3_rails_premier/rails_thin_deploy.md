# rails+thin服务部署

rails的 运行方式：  3种：
1. development.   日常开发使用的模式。 该模式下，我们对于rb文件的改动，会立即生效
（不用重启服务器了！ ）
不要小看这一点。 在其他的语言，其他的框架中，都要重启的。
（java => tomcat.   python => pyramid. )
这个特性，能为我们省太多的事儿了。

注意： 开发模式下，如果修改 config 目录下的内容， 就一定要重启服务器。

2. production

生产模式。部署的时候，务必使用这个模式。
生产模式下，rails的所有文件，都会一次性的被加载到内存中。 所以它运行起来，速度
特别快。（development模式，则是： 每次接受到请求时，都会 重新加载资源，所以就慢，
）所以，该模式下，我们对rb文件的改动，必须重启之后才能生效。

另外，在Rails 3.0以后，引入了： asset pipline, （会分别压缩多个css，js文件到一个大文件中）
所以，在这个版本以后，部署就多了一些内容。

3. test

一般人用不上。 但是我们要用。 这个是测试环境。 在运行单元测试的时候，使用。
它的特点： 每次运行测试前，数据库的内容都会清空。



## development 模式：

$ bundle exec rails server

它默认：
1. 跑在本地
2. 端口： 3000
3. 方式： development


## production 模式。

1. 最low的模式： $ bundle exec rails server -e production
最入门，也最low。 它用的服务器，是 rails自带的服务器。 性能不好。
同时10个请求访问，就会卡。所以不要用。
什么时候用呢？ 做 测试部署 的时候，可以用。 用这个命令，可以快速的判定，当前
的环境能否适合部署。

2. 最常见的模式：  使用 (应用服务器）thin/unicorn/passenger + (静态服务器）nginx 的方式来部署。
应用服务器:  专门解析rails的服务器。 （例如： erb => html )
静态服务器： 跟应用的语言无关。 只处理静态的资源。 （例如：
返回  jpg 图片。
返回  css
以及返回 静态的 html 页面 (500, 404）
以及： 返回  "应用服务器” 处理好的 html 页面）


策略：

1. 请求的是： rails 的资源, 那么请求就会 先被 nginx 拦截。
之后，nginx发现；这个资源应该由  rails服务器来处理。 再把请求交给
 rails服务器。
 rails服务器处理好之后， 把结果返回给nginx, nginx再把结果返回给浏览器。

2. 如果请求的是： css/js/普通的html页面， 那么 nginx 就直接处理，返回给浏览器。

browser          |    nginx               |        thin
request(1个)     <=>    80端口(一个请求）  <=>    3001
                                           <=>    3002
                                           <=>    3003
                                           <=>    3004

## 几个rails服务器的对比

1. passenger :  是跟nginx 结合的特别密切的服务器。 不建议使用。
普通使用： 没问题。
缺点：
如果一台服务器上，部署了10个rails 的项目。 那么：
每次重启passenger, 10个项目都要一起重启。 或者： 一起关闭。这个是不合理的。
问题在下列情况会特别凸显：
例如： 在优酷，当时我们有20个rails子项目。 其中19个，访问量，占了 0.04%。
另外一个应用的访问量： 99.96% .   它必须 24x7的跑着。不能有当机时间。30秒
都不行。

那么问题来了：  我只希望部署一个rails应用，但是，passenger 要求我强制重启
20个rails应用，每次整体一重启, 各种报警全面飙红（在运维同学的后台上），相关
的负责人，手机卡卡报警短信。

所以，不要用passenger. 无论rails的作者怎么鼓吹（现在也不提了）。

2. unicorn . 我没用过。不提了。 但是看过别人用。配置麻烦，调试麻烦，不适合新手。
效率也见的提高多少。

3. webrick rails 自带的服务器。 官方不提倡使用。它的性能不好。 优点：
直接上手 。 加个参数 就可以用了：  $ bundle exec rails server -e production

4. mongrel:  thin 的前身。 10年以前很多人用。 后来 mongrel 不会被维护了。

5. thin. 提倡使用。 优点：
  1. 上手特别简单。$ bundle exec thin start/stop/restart -C config.yml
  2. 性能特别好。
  3. 调试方便。
  4. 非常方便的可以与nginx做整合。

## 如何使用thin?

官网： http://code.macournoyer.com/thin/

1. 在Gemfile中，安装 这个 gem：

```
gem 'thin'
```
2. $ bundle install

3. $ bundle exec thin start
Using rack adapter
Thin web server (v1.7.0 codename Dunder Mifflin)
Maximum connections set to 1024
Listening on 0.0.0.0:3000, CTRL+C to stop

以上3步，就可以: 默认启动在 3000端口， 使用development模式。


4. 使用 production模式
虽然 thin 也支持 各种参数（修改port, host , environment ... ）跟 rails server一样，
但是我们在实战中，使用 配置文件（最大的好处： 一次可以启动多个thin进程，跑在不同
的端口上）

配置文件如下：

# 文件名： config/thin.yml
```
---
chdir: /opt/app/siwei.me/current   # 你的rails 应用的所在目录
environment: production    # 指定了 是 production模式
address: 0.0.0.0
port: 3001                 # 端口号。
timeout: 30
max_conns: 1024
max_persistent_conns: 100
require: []
wait: 30
servers: 2                 # 很重要。希望启动的thin 的进程数。
daemonize: true
```
上面，需要注意的，就是几个：

chdir.
environment：
port: 起始的端口号。 例如： 3001
servers: 希望启动的thin 的总进程数。 例如：我写成4. 那么启动thin之后，
就会有4个thin的进程，分别运行在：  3001, 3002, 3003, 3004 端口上。

配置文件写好后， 启动 它:

```
$ bundle exec thin start -C config/thin.yml
Starting server on 0.0.0.0:3001 ...
Starting server on 0.0.0.0:3002 ...
Starting server on 0.0.0.0:3003 ...
Starting server on 0.0.0.0:3004 ...
```

记得：确保在 config/secrets.yml 中，为 production: secret_key_base
加上内容， 最好是随机生成：
```
production:
  secret_key_base: slkfjdalksjflaksdjflaskjdfklasjdflaksdjfalsjdfaklsdfjlaskdfjlasd
```

调试： 看log.

log会被放在 log目录下。 例如：

```
$ ls log
development.log  thin.3001.log	thin.3003.log
production.log	 thin.3002.log	thin.3004.log
```

当你的rails项目没有跑起来时，就来这里看log。

```
$ ps -ef | grep thin
kaikai   21020     1  8 10:06 ?        00:00:02 thin server (0.0.0.0:3001)
kaikai   21030     1  9 10:06 ?        00:00:02 thin server (0.0.0.0:3002)
kaikai   21040     1  9 10:06 ?        00:00:02 thin server (0.0.0.0:3003)
kaikai   21052     1  7 10:06 ?        00:00:02 thin server (0.0.0.0:3004)
kaikai   21069 19171  0 10:06 pts/2    00:00:00 grep thin
```


优酷的项目， 都是  nginx + thin

### 一.将项目部署到服务器上面一般有两大类方式:

#####  a:手动部署;

#####  b:借助capistrano或者其它工具进行快速部署.

  (注:在本章节中,只讲述了手动部署的过程,使用capisrano工具快速部署详见博客).

### 二.首次部署的过程

a: 将代码放到指定服务器的指定位置

b: 启动服务器上面的项目

c: 在负载均衡服务器上为项目设置request跳转指向

### 手动部署过程的例子

###### 例子需要的一些配置:

###### 假设服务器域名:ubuntu@servers.test.co 跳板端口号为:43250

###### 假设项目后,访问项目所用的网站域名(网址):hellotest.co

###### 假设项目将放在服务器主机: 192.168.33.120上面

###### 假设项目启动时所用的端口号: 8888

###### 假设服务器中负载均衡的主机地址: 192.168.33.88

#### 1.登陆服务器

打开命令行,使用ssh登陆服务器

$ ssh 服务器域名 -p 端口号

```
  $ ssh ubutu@servers.test.co -p 35250
```
#### 2.跳转到项目所要放置的服务器主机

$ ssh 项目所在的服务器主机
```
  $ ssh 192.168.33.120
```

#### 3.进入到项目位置,并将github或者gitcafe上面的项目clone到所在位置

 $ cd 项目位置

##### 克隆项目代码

 $ git clone github(gitcafe) 项目源地址

#### 4.进入到项目文件下,使用vim修改config目录下的数据库文件配置和thin服务文件配置

$ cd 项目文件

##### 配置数据库
```
  $ vim config/database.yml
```
例子:
```
  default: &default
    adapter: mysql2
    pool: 5
    timeout: 5000
    # 数据库用户名
    username: root
    # 数据库密码
    password: 123456
    # 数据库地址
    host: localhost

  # 设置development和production模式下的数据库配置
  development:
    <<: *default
    # 数据库的名字
    database: yuehouse

  production:
    <<: *default
    database: yuehouse
```
##### 配置thin服务
```
  $ vim config/thin.yml
```
例子:
```
  ---
  # 项目的根目录路径
  chdir: /opt/app/pc_mall_test/yuehouse_mall_to_customers
  # 启动模式
  environment: production
  address: 0.0.0.0
  # 启动时的端口号
  port: 3663
  timeout: 30
  # log文件路径
  log: /opt/app/pc_mall_test/yuehouse_mall_to_customers/log/thin.log
  pid: tmp/pids/thin.pid
  max_conns: 1024
  max_persistent_conns: 100
  require: []
  wait: 30
  threadpool_size: 20
  daemonize: true
  # 启用服务端口号的个数(从设置的port开始,端口号依次+1)
  servers: 2

```

#### 5.配置完成后,启动项目
```
  $ bundle
```
##### 编译项目中的js文件和图片等
```
  $ bundle exec rake assets:precompile RAILS_ENV=production
```
##### 使用thin服务启动项目,根据情况不同可能会出现延迟
```
 $ bundle exec thin start config/thin.yml
```

#### 6.为项目建立跳转指向

登陆到负载均衡服务器

$ ssh 负载均衡服务器主机地址
```
  $ ssh 192.168.33.88
```

##### 编辑负载均衡服务器上面的nginx的配置文件
###### 目的:监听用户发送的http请求(端口为80),如果为请求域名(网址)为项目域名(网址),则将此请求转到项目所在的服务器主机的项目端口上,因此项目才能根据用户的请求做出相应的答复
```
  $ vim /etc/nginx/nginx.conf
```
##### 在http{}中添加如下代码:
例子:
```
  server {
          # 监听80端口的用户请求
          listen       80;
          # 判断发送请求的域名(网址)
          server_name  hellotest.co;
          charset utf-8;
          location / {
              proxy_pass          http://yuehouse_mall_servers;
              proxy_redirect      default;
              proxy_set_header    x-forwarded-for $proxy_add_x_forwarded_for;
              proxy_set_header    x-real-ip $remote_addr;
              proxy_set_header    host $http_host;
              proxy_next_upstream http_502 http_504 error timeout invalid_header;
          }
  }

  upstream yuehouse_mall_servers{
         # server 项目所在位置的主机地址:项目端口号
         server 192.168.33.120:8888;
  }
```

#### 7.配置完请求跳转,保存并退出,然后需要重启才能生效
##### 检查nginx配置是否出现语法错误
```
  $ nginx -t
```
##### 如果有错误,修改错误;

##### 没有错误就重启nginx服务器使刚才的配置生效,即:
```
  $ nginx -s reload
```
##### 配置生效后,如果第5步项目没有启动,则跳转到项目服务器,进入到项目所在目录,启动项目(操作过程详见第5步);如果第5步的项目已启动,则在浏览器中输入项目域名(网址),即可访问项目网页.

##### (thin服务启动后:使用命令行可以测试项目是否启动)

$ curl 项目域名(网址)
```
  curl hellotest.co
```

### 三. 如果项目已经部署过了,只更新代码

##### 登陆到项目所在的服务器主机,并进入到项目目录下,具体操作详见上面的步骤1.2

##### 将github(或gitcafe)上面已经更新过的代码pull下来

$ cd 项目文件
```
 $ git pull origin master
```
#### 编译js文件,并重启项目的thin服务
```
 $ bundle
```
##### 编译文件(如果改动的代码中不包含js文件,此步骤可跳过)
```
 $ bundle exec rake assets:precompile RAILS_ENV=production
```
##### 重启项目(可能需要几秒钟)
```
 $ bundle exec thin restart config/thin.yml
```
##### 重启成功后就可以访问网页了
