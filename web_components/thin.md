# Rails服务器：Thin

Thin 是Rails的服务器，它实现了event machine, 理论上可以达到node一样的效果。
（不过这个要看具体的逻辑代码）

比起Rails自带的Webrick 是好上太多。

在最初的日子，还有mongrel, passenger 等，现在又多了一些unicorn 等服务器。

掌握好Thin, 再考虑其他的。

## 安装

$ gem install thin

## 基本用法

跟rails server 命令一样：

$ thin start|stop|restart

在4000端口上启动：

$ bundle exec thin start -p 4000

更多参数请看 文档。

## 使用配置文件启动

假设配置文件放在 config/thin.yml ：

```yml
---
chdir: '/opt/app/current'  #  这里需要修改。
environment: production
address: 0.0.0.0
port: 6661
timeout: 30
log: log/thin.log
pid: tmp/pids/thin.pid
max_conns: 1024
max_persistent_conns: 512
require: []
wait: 30
servers: 4
daemonize: true
```

我们就可以这样使用了：

```
$ bundle exec thin start -C config/thin.yml
```

上面的意思是， 让你的Rails应用（部署在 /opt/app/current 目录下）,
启动在 4个端口（6661~6664)上，并且都是已 daemon 的形式来运行

停止时，可以使用 `thin stop -C config/thin.yml`, 也可以直接`killall thin`

## Thin 跟 Nginx配合使用

nginx的配置文件为：

(如果你是用Ubuntu, 那么nginx配置文件 一般都是  `/etc/nginx/nginx.conf` ）
```
   server {
       listen 80;
       charset utf-8;
       location / {
           proxy_pass          http://rails_servers;
           proxy_redirect      default;
           proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header    X-Real-IP $remote_addr;
           proxy_set_header    Host $http_host;
           proxy_next_upstream http_502 http_504 error timeout invalid_header;
      }
   }
   upstream rails_servers{
          server 127.0.0.1:6661;
          server 127.0.0.1:6662;
          server 127.0.0.1:6663;
          server 127.0.0.1:6664;
   }
```
修改好后， 重启：
```
  $ nginx -t  （测试一下配置文件）
  $ nginx -s reload
```

然后保证本地的6661~6664四个端口都有thin启动好，访问本地的80端口，
nginx就会把请求转发到任一一个rails端口上。

## 注意
- 原则上，thin的启动进程数，跟CPU的core数目一致，例如4核CPU，就开四个thin进程。
- thin以daemon 形式启动时, 我们要观察log, 因为它如果遇到错误了，不会打印在控制台中。
