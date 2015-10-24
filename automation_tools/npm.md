# npm : node package manager

## 介绍

跟ruby中的rubygem 一样， npm 是 node 的package管理工具。有了它，我们就能特别方便的安装第三方包。

## 基本用法

帮助:

```bash
$ npm -h
$ npm help install 
```

安装:

```bash
$ npm install stss
```

卸载:

```
$ npm uninstall stss
```

查看版本:

```
$ npm ls
```

生成package.json:

```
$ npm init
```

更新 package: 

```
$ npm update <stss> (如果不写的话, 就会更新全部的 package) 
```

## 设置代理

由于npm 的服务器在国外， 所以很多时候npm　需要设置代理才能正常安装。
光设置　`$ export http_proxy=... ` 是不行的。所以：

```bash
$ npm config set proxy http://proxy.company.com:8080
$ npm config set https-proxy http://proxy.company.com:8080
```

或者：

```bash
$ npm --https-proxy=http://proxy.company.com:8080 -g install karma
```

或者执行命令前，

```bash
export npm_config_proxy http://proxy.company.com:8080
export npm_config_https_proxy http://proxy.company.com:8080
```

然后　

```bash
$ npm install -g mocha --verbose
```

只要看到最后是个　'ok' 就表示成功啦
