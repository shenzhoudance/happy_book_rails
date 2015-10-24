#rubygem

##简介

对于使用ruby语言的程序员来说，我们一定会少不了经常性的和rubygem打交道，那么到底什么是rubygem呢？要说清楚这个问题我们首先需要知道什么是gem，
gem通俗的讲就是一种文件的组织形式，ruby将许多通用的开发工具包和第三方插件都做成了一个个的gem包,利用这些gem包我们可以很方便的就能开发一些我
们的应用，我们众所周知的rails开发框架其实也就是一组比较完整的rubygem包。rubygems则是一个ruby的包管理器,提供了便捷的分发和管理gem包的工具，
以及用于分发gem包的服务器。从ruby的1.9版本开始rubygems已经成为ruby标准库的一部分。

rubygems.org是ruby官方的gem托管中心，在国内我们一般使用的是淘宝的镜像源https://ruby.taobao.org
需要了解更多的同学可以去gem官方的托管中心，也就是<http://rubygems.org>查看更多的资料。

##gem的使用

查看当前的gem版本
```bash
$ gem -v
```

安装gem包

```bash
$ gem install gemname (需要安装的gem包的名字)
```

卸载gem包

```bash
$ gem uninstall mygem
```

列出本地已安装的gem包
```bash
$ gem list --local
```

列出远程的gem包
```bash
$ gem list --remote
```

查询带有某个关键字的gem包
```bash
$ gem list -r keyword
```

获取gem帮助命令

```bash
$ gem help
```

获取一些gem的事例命令
```bash
$ gem help example
```

###获取更多的gem命令可以查看官方的资料<http://guides.rubygems.org/command-reference>

