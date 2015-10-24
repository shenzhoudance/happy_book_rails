TODO: 内容未完成

>http://dockerpool.com/static/books/docker_practice/install/ubuntu.html

>http://dockone.io/article/179

>https://github.com/docker/docker


# Docker入门

## 安装
> 关于其他系统下的安装方式，下面的链接都有说明：
> https://docs.docker.com/installation


###mac安装

> 参考文献: https://docs.docker.com/installation/mac/

这是官方网站上面的例子:

- [download](https://www.docker.com/toolbox) Docker Toolbox

###使用

- 从**Launchpad**中打开新安装的**Docker Quickstart Termina**，它会做了下面的事情
	- 打开一个termina
	- 如果**VM**不存在则创建一个
	- 配置**VM**环境 
- 接下来就能运行docker命令了,我们可以跑一个hello-world的容器,如下所示:

```console
$ docker run hello-world
```
```console
输出:
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world

b901d36b6f2f: Pull complete 
0a6ba66e537a: Pull complete 
Digest: sha256:517f03be3f8169d84711c9ffb2b3235a4d27c1eb4ad147f6248c8040adb93113
Status: Downloaded newer image for hello-world:latest

Hello from Docker.
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/userguide/

```
`$docker-machine ls`查看docker-machine的列表:

```console
输出:
NAME      ACTIVE   DRIVER       STATE     URL                         SWARM
default   *        virtualbox   Running   tcp://192.168.99.100:2376  
```
启动一个叫web的nginx,过程中会下载一些内容:

```console
$ docker run -d -P --name web nginx
```
此时输入:

```bash
$ docker port web
```
```
输出:
443/tcp -> 0.0.0.0:32768
80/tcp -> 0.0.0.0:32769
```
这时候访问localhost:32769是没有内容的，我们需要再给它设置一个默认的ip

```bash
$ docker-machine ip default
```
```
输出:
192.168.99.100
```
此时访问:192.168.99.100:32769就可以看到nginx的应用了。

![doctor_nginx](/images/doctor_nginx.png)

如果想要移除运行的nginx则需要先stop后rm nginx:

```bash
$ docker stop web
$ docker rm web
```

# 使用Docker部署Rails应用




