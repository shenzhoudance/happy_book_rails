# 引言

要有自我学习能力！

我们作项目，90%的时候，都要用别人的轮子。 不要自己作。

举个例子：

轮播图。

在10年前， 没有人手写轮播图。  都是拿来主义。 现在，有了jQuery, 写轮播图，也是个麻烦事儿。
所以，我们要： 会用别人的轮子。

## 怎么发现轮子？

对于技术层面的轮子，不要baidu。 一定要英文google。 因为：国外的轮子是最圆的。

举个例子：
  国内有个日历控件。  m91 ? 很奇怪的名字。 国外： jquery-ui : datepicker.
后者在质量上还是在UI上， 都碾压前者。

关键： 要知道 轮子的英文名称。

轮播图。 slider
日历     心里认为： calender,   事实上叫： date picker
提示框。 （背景变黑， 中间出现个弹窗）国内目前没有统一的叫法。 国外： modal dialog.
标签页： tabs
标签： tag
分页： pagination

我们要知道 一系列的名字，才能google 。

## 从标准内容看项目


补充：  每个插件，必然有自己的官方网站。  例如：  jquery-validate
很有可能有个自己的独立的网站：  https://jqueryvalidation.org/
除了官方网站， 还会有自己的github ( 源代码位置） https://github.com/jzaefferer/jquery-validation
而且：还要有文档。 文档分成两种:
1. 入门文档（ guides, tutorial, document ) ,例如： https://jqueryvalidation.org/documentation/
2. API 文档（ https://jqueryvalidation.org/documentation/#link-api-documentation)

还要有个DEMO：
https://jqueryvalidation.org/files/demo/

对于Rails和很多其他组件来说， 文档 跟 API 是分开的。例如：

http://guides.rubyonrails.org/
http://api.rubyonrails.org/

## 如何使用轮子

1. 找到官方站点。
2. 查看官方文档和例子
3. 照着做就可以了。


举个例子：  我们希望某个web页面， 里面有很多图片， 在最初加载的时候， 使用一个默认图片来代替它。
（你们看看 今日头条， 就知道这个感觉了）

google:  web images load jquery
（为什么要用jquery呢？ 因为我们用的js框架就是jquery)
我会发现很多页面， 出现了一个关键字： lazy . 所以，我们就可以把这个关键字，记录下来，再次搜索：

web images lazy load jquery

然后，结果能更加精准一些。

我们找到了： https://responsivedesign.is/resources/javascript-jquery/lazy-load-jquery
（全都是英文）

看了3分钟， 找到了两个关键点：

1. demo地址： http://www.appelsiini.net/projects/lazyload/enabled.html
2. github地址： https://github.com/tuupola/jquery_lazyload

github地址找到了，就可以认为它的官方地址找到了。

如何安装，如何使用，就都知道了。


