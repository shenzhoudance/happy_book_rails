# rails+thin服务部署


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
