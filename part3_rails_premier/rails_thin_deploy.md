# rails+thin服务部署


## 介绍.
rails的 运行方式：  3种：

### 1.development.

日常开发使用的模式。 该模式下，我们对于rb文件的改动，会立即生效 （不用重启服务器了！ ）

不要小看这一点。 在其他的语言，其他的框架中，都要重启的。 （java => tomcat.   python => pyramid. )

这个特性，能为我们省太多的事儿了。

注意： 开发模式下，如果修改 config 目录下的内容， 就一定要重启服务器。

### 2.production

生产模式。部署的时候，务必使用这个模式。

生产模式下，rails的所有文件，都会一次性的被加载到内存中。 所以它运行起来，速度
特别快。（development模式，则是： 每次接受到请求时，都会 重新加载资源，所以就慢，
）所以，该模式下，我们对rb文件的改动，必须重启之后才能生效。

另外，在Rails 3.0以后，引入了： "asset pipline "

所以，在这个版本以后，部署就多了一些内容。(多运行一个命令)

### 3.test

一般人用不上。 但是我们要用。 这个是测试环境。 在运行单元测试的时候，使用。
它的特点： 每次运行测试前，数据库的内容都会清空。

### 针对3个环境的配置文件

打开 `config/environments` 文件夹，可以看到3个配置文件。

```
▾ config/
  ▾ environments/
      development.rb
      production.rb
      test.rb
```

每个配置文件，对应一种 rails的 运行模式。

打开 config/database.yml, 也可以看到针对3个环境的不同配置：

```
 development:
   adapter: mysql2
   database: my_library
   username: root
   password: 666
   host: localhost

 production:
   adapter: mysql2
   database: my_library
   username: root
   password: 666
   host: localhost

 test:
   adapter: mysql2
   database: my_library
   username: root
   password: 666
   host: localhost
```


## 以development 模式运行

```
$ bundle exec rails server
```

它默认：

1. 跑在本地
2. 端口： 3000
3. 方式： development

启动后,在浏览器中输入 `http://localhost:3000` 即可访问.

## production 模式。

下面是两种进入到production模式的办法：

### 1. 最快但是不推荐的办法

```
$ bundle exec rails server -e production
```

最入门，也最low。 它用的服务器，是 rails自带的服务器(webrick)。 性能不好(类似于数据库中的 sqlite)。
同时10个请求访问，就会卡。所以不要用。

而且css, images, 都不会正常显示出来.

优点: 做 测试部署 的时候，可以用。 用这个命令，可以快速的判定，当前的环境能否适合部署。


### 2. 最常见的模式

使用 (应用服务器）`thin/unicorn/passenger` + (静态服务器）`nginx` 的方式来部署。

- 应用服务器:  专门负责生成动态的内容(例如：rails/java)的服务器。 （例如： erb => html )

- 静态服务器： 跟应用的语言无关。 只处理静态的资源。 （例如：

  - 返回  jpg 图片。
  - 返回  css
  - 返回 静态的 html 页面 (500, 404）
  - 返回  "应用服务器” 处理好的 html 页面）

也就是说， 静态服务器，可以直接 运行 静态站点。


策略：

1.请求的是： rails 的资源, 那么请求就会 先被 nginx 拦截。
之后，nginx发现；这个资源应该由  rails服务器来处理。 再把请求交给
rails服务器。

rails服务器处理好之后， 把结果返回给nginx, nginx再把结果返回给浏览器。

2.如果请求的是： css/js/普通的html页面， 那么 nginx 就直接处理，返回给浏览器。

```
browser          |    nginx               |        thin
request(1个)     <=>    80端口(一个请求）  <=>    3001
                                           <=>    3002
                                           <=>    3003
                                           <=>    3004
```

## 几个rails服务器的对比

### 1. passenger

是跟nginx 结合的特别密切的服务器。 不建议使用。

普通使用： 没问题。

缺点：
如果一台服务器上，部署了10个rails 的项目。 那么：
每次重启passenger, 10个项目都要一起重启。 或者： 一起关闭。这个是不合理的。

问题在下列情况会特别凸显：

例如： 在优酷，当时我们有20个rails子项目。 其中19个，访问量，占了 0.04%。
另外一个应用的访问量： 99.96% .   它必须 24x7的跑着。不能有当机时间。30秒都不行。

那么问题来了：  我只希望部署一个rails应用，但是，passenger 要求我强制重启
20个rails应用，每次整体一重启, 各种报警全面飙红（在运维同学的后台上），相关
的负责人，手机卡卡报警短信。

所以，不要用passenger. 无论rails的作者怎么鼓吹（现在也不提了）。

### 2. unicorn

我没用过。不提了。 但是看过别人用。配置麻烦，调试麻烦，不适合新手。
执行效率也没见的提高多少。

### 3. webrick

rails 自带的服务器。 官方不提倡使用。它的性能不好。 优点：
直接上手 。 加个参数 就可以用了, 例如：

```
$ bundle exec rails server -e production
```

### 4. mongrel

`Thin` 的前身。 10年以前很多人用。 后来 mongrel 项目停掉了。 用起来跟Thin一样.

### 5. Thin.

提倡使用。 优点：

5.1 上手特别简单。`$ bundle exec thin start/stop/restart -C config.yml`
5.2 性能特别好。
5.3 调试方便。
5.4 非常方便的可以与nginx做整合。

## 如何使用Thin?

官网： http://code.macournoyer.com/thin/

1.在Gemfile中，增加内容：

```
gem 'thin'
```

2.安装gem:

```
$ bundle install
```

3.启动

```
$ bundle exec thin start
Using rack adapter
Thin web server (v1.7.0 codename Dunder Mifflin)
Maximum connections set to 1024
Listening on 0.0.0.0:3000, CTRL+C to stop
```

这时, Rails服务器默认启动在 3000端口， 使用development模式。

4.使用 production模式

虽然 thin 也支持 各种参数（修改`port`, `host` , `environment` ... ）跟 `rails server` 一样，

但是我们在实战中，使用配置文件来运行.（最大的好处： 一次可以启动多个thin进程，跑在不同
的端口上）

配置文件如下：

```
# 文件名: config/thin.yml
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

- `chdir`: 进程的初始位置。务必与rails项目路径相同。
- `environment`： production/development
- `port`: 起始的端口号。 例如： 3001
- `servers`: 希望启动的thin 的总进程数。 例如：我写成4. 那么启动thin之后，
就会有4个thin的进程，分别运行在：  3001, 3002, 3003, 3004 端口上。


启动production模式的Rails 之前, 记得在 `config/secrets.yml` 中，加上以下内容:
```
production:
  secret_key_base: a1b2c3d4a5d6...z100 #该内容应该随机生成
```

### 启动Thin

配置文件写好后， 启动 它:

```
$ bundle exec thin start -C config/thin.yml
Starting server on 0.0.0.0:3001 ...
Starting server on 0.0.0.0:3002 ...
Starting server on 0.0.0.0:3003 ...
Starting server on 0.0.0.0:3004 ...
```

### 停止Thin

```
$ bundle exec thin stop -C config/thin.yml
```

### 重启Thin

```
$ bundle exec thin restart -C config/thin.yml
```


## 使用 thin + nginx 来部署。

前提：

1. 要使用 asset pipeline. 对现有的 asset pipeline进行编译:

```
$ bundle exec rake assets:precompile RAILS_ENV=production
```

以上操作大约耗时1-15分钟不等. 看你用的asset pipeline如何了.
它会生成大量的assets 编译后的文件.

2. 在nginx中，进行设置。 让nginx来响应对于 css， js的请求。

修改 nginx的配置文件如下：( 注意： `location ~ ^/assets/` 这句话。 )

```
  server {
          listen       80;
          server_name  railsgirlsbeijing.com;
          charset utf-8;
          location / {
              proxy_pass          http://rails_girls_site;
              proxy_redirect      default;
              proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header    X-Real-IP $remote_addr;
              proxy_set_header    Host $http_host;
              proxy_next_upstream http_502 http_504 error timeout invalid_header;
          }
          # 这句话，最重要。
          # 表示，所有url中，以 /assets 开头的 url， 都会被nginx所响应。
          location ~ ^/assets/ {

            # 表示，处理这些请求的话，应该从哪个文件夹开始。
            root /opt/app/siwei/rails_girls_cn/shared;
            expires 1y;
            add_header Cache-Control public;
            add_header ETag "";
            break;
          }
  }
  upstream rails_girls_site{
    server localhost:3560;
    server localhost:3561;
  }
```

记得在 config/environments/production.rb文件中：

```
Cms::Application.configure do
    # 不让 rails 来处理 /assets 开头的 url, 这些要交给nginx来处理.
    config.serve_static_assets = false
end
```

修改好配置后, 记得重启nginx:

```
$ sudo nginx -s reload   # 重启服务器
```

## 调试Rails 服务器:

手段是看Log(日志).

不管你用哪种服务器(thin, passenger ... ), 日志会被放在 `log`目录下。 例如：

```
$ ls log
development.log  thin.3001.log	thin.3003.log
production.log	 thin.3002.log	thin.3004.log
```

当你的rails项目没有跑起来时，就来这里看log。

```
$ ps -ef | grep thin
siwei   21020     1  8 10:06 ?        00:00:02 thin server (0.0.0.0:3001)
siwei   21030     1  9 10:06 ?        00:00:02 thin server (0.0.0.0:3002)
siwei   21040     1  9 10:06 ?        00:00:02 thin server (0.0.0.0:3003)
siwei   21052     1  7 10:06 ?        00:00:02 thin server (0.0.0.0:3004)
siwei   21069 19171  0 10:06 pts/2    00:00:00 grep thin
```

优酷的项目， 都是  `nginx + thin`

## 问题： 开多少个thin进程合适？

有多少个CPU内核，就开多少个thin进程。
例如： 4核CPU， 一般开： 4个 thin 进程。（同理， nginx的  worker也是一样）

# 作业：

1. 在服务器上， 建立一个静态站点。
2. 在服务器上， 运行 rails的production 模式。

# nginx 内容： 见 operation.siwei.tech

