# Layout(布局)

## 概念

所有的action 在渲染页面的时候，都会把动态内容的外层，包裹一个HTML页面。
这个HTML页面，就是一个layout.

都放在 `app/views/layouts/` 目录下.

## 例子

从一个例子看起：

假设我们有两个页面：

page1.html.erb

```
<html>
  <head>
    <title>图书管理系统</title>
    <script src='jquery.js'></script>
  </head>
  <body>
    <div> 我是页面1 </div>
    <div class='footer'>
      copyright(at)2015 xx.co.ltd
    </div>
  </body>
</html>
```

page2:

```
<html>
  <head>
    <title>图书管理系统</title>
    <script src='jquery.js'></script>
  </head>
  <body>
    <div> 我是页面2 </div>
    <div class='footer'>
      copyright(at)2015 xx.co.ltd
    </div>
  </body>
</html>
```

可以看出，代码重复率，高达90%。

我3年前，在优酷接手过一个遗留项目。做了半年，新功能增加无数。到最后一统计，代码量从4万行，变成2万行。

去掉的，就是上面两个示例页面的重复代码。

怎么去掉的呢？ 用 Layout (布局).

## 解决办法

把公共的代码提取出来：

新建一个页面： `app/views/layouts/application.html.erb`:

```
<html>
  <head>
    <title>图书管理系统</title>
    <script src='jquery.js'></script>
  </head>
  <body>
    <%= yield %>
    <div class='footer'>
      copyright(at)2015 xx.co.ltd
    </div>
  </body>
</html>
```

其中的 `<%= yield %>` 区域，规定了会变化的内容。其他的内容，都是固定的。

这就是最简单的也是最常见的layout.

使用方式：

在 `controller` 中， 默认就会加载  `application.html.erb` 这个文件，作为layout.
也可以显式的给它标记出来：

```
class BooksController < ApplicationController
  # 这个是Rails的惯例，其实不用写。
  layout 'application'
end
```

## 如何使用不同的layout?

例如：  在不同的 controller中使用 不同的 layout：

```
class BooksController < ApplicationController
  layout 'one'
end

class FruitsController < ApplicationController
  layout 'two'
end
```


## 在同一个 controller中，不同的action使用不同的layout:


```
  def index
    render layout: "two"
  end

  def edit
    render layout: "one"
  end
```

## 我不想使用layout呢？

可以如下面所示:

```
  def index
    render layout: false
  end
```


## 什么是`<%= yield %>` ?

回答： yield 是把某个block调用后， 在该位置显示 结果。
也就是，所有你们写的 erb文件的选然后的内容，最后都嵌到了这个 layout中。

(带领大家想一下当初的ruby基础)
例如, 我们可以定义一个ruby方法：

```
def execute_code
  puts "=== before ==="
  yield
  puts "=== after ==="
end
```
然后,执行它:

```
execute_code do
  puts "我是一段来自block的代码"
end
```

结果：

```
=== before ===
我是一段来自block的代码
=== after ===
```

## 布局中，如何放 js, css ?

1.按照传统的形式直接写上去。
```
<link href="http://cdn.dfile.cn/v/1434451523/i1/css/dict.min.css" media="screen" rel="stylesheet" type="text/css" />
```

2.使用 `javascript_include_tag` 和 `stylesheet_link_tag`

例如，原始的html:
```
<script src="./static/javascript/jquery.min.js"></script>
<script src="./static/javascript/jqueryui.min.js"></script>
<script src="./static/javascript/bootstrap.min.js"></script>
```

在rails当中，往往把所有的js文件，都放到 `app/assets/javascripts` 目录下。

然后：
```
<%= javascript_include_tag 'jquery.min.js' %>
<%= javascript_include_tag 'jqueryui.min.js' %>
<%= javascript_include_tag 'bootstrap.min.js' %>
```

最大的优点：`js/css` 在不同的模式下（development, production) 的路径是不同的。
rails 会自动识别它的路径。

以上两种办法法不推荐，一旦引用的外部js/css写多了，会直接拖慢页面打开速度.

3.使用asset pipeline

见下一章的内容.

## 实战中不用学的

其他的都不用学。

