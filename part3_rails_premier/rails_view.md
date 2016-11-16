# 阅读本文收获

- 知道Rails中的erb文件跟 JSP, PHP是一样的.
- 知道如何使用controller中的变量
- 知道partial ( 片段)

# View的基本概念和用法

View在MVC中,一直是最简单的概念. 大家要分分钟入门.

视图在Rails中, 就是 .html.erb 文件. 我们原则上把跟HTML有关的东西,都写在 视图中.

每个视图都要由controller触发. 所有的视图文件,都放在: `app/views/` 目录下. 例如: `app/views/books/new.html.erb` 这是Rails的约定 .

最基本用法

下面是一个最简单的erb文件:

```
<p>当前时间是:  <%= Time.now %></p>
```
它会直接被转换成下面的HTML:

```
<p>当前时间: 2016-10-08 21:01:35 +0800</p>
```

# `<% %>` 与 `<%= %>` 的区别

`<% %>` 表示仅运行代码, 例如: `<% a = 1 %>`
`<%= %>` 表示,不但运行, 而且把结果渲染到html中. 例如: `<%= Time.now %>`

# Rails中的视图在什么时候被渲染?

Rails架构： M - V - C（几乎是所有经典web项目的架构）. 其中:

- Model: 操作数据库。
- Controller: 把每个 "http request" 分发到对应的 "Action( method)"来处理
- View: 显示 HTML 页面。

所以，我们用一个例子来说明, view在哪里建立,在什么时候渲染：

1.小王同学在浏览器端，输入了一个网址: http://server.com/fruits/new ，回车。(这会产生一个 "http request" , 请求方式是 GET)

2."http request" 从浏览器，发送到服务器端(server.com)之后，
Rails就会 把这个请求交给 router 来处理。

(接下来的事儿，都发生在 服务器端）

3.router根据配置文件： config/routes.rb 中的配置：

```
Rails.application.routes.draw do
  resources :fruits # 根据这个路由配置
end
```

把这个request，分发到： `fruits` controller 中的 `new` action.

4.new action 做一些 处理， 显示对应的 erb .

```
# 下面是 app/controllers/fruits_controller.rb 的内容:
class FruitsController < ApplicationController
  def new
    @hello = 'hellow, Rails!'
    # 啥也不写，就直接渲染对应的 erb页面:
    #  app/views/fruits/new.html.erb
  end
end
```

5. 上面的 `new` action 执行完, 会自动渲染 new.html.erb这个文件( 文件路径: `app/views/books/new.html.erb` )
(为什么PHP 很好入门？ 就是因为，PHP 上来就让你写这个）

```
<% [1,2,3].each do e %>
  <%= e %> <br/>
<% end %>
```

上面的视图文件会被渲染成:

```
1 <br/> 2<br/> 3<br/>
```
(为什么PHP 入了门之后，就发现 到处都是坑？ 就是因为PHP
中， 只有这个View(如果不使用框架的话).
同理， JSP， ASP 稍微好点，但是也是一样。）

基本概念讲完了,就是这么简单.

# 恰当的使用 @变量
大家记得, 任何 实例变量 (@name 这样的), 都用于定义在controller中,然后在 view中被调用.

# Partial (片段)
有时候，如果某个erb文件， 过于复杂了。 例如： 20行。

或者， 某些代码可以重用。

我们就用 Partial 来简化我们的代码,提取出公共的部分.

## 不带参数的partial
例如:


```
<!-- 下面这段是版权声明，多个页面都需要重用  -->
<footer>
  copyright@2016 xx.co.ltd
</footer>
```

那么就把它写成一个 partial (片段）

全名是: `app/views/fruits/_footer.html.erb`(注意，文件名以 "_"开头)

然后，我们就可以在对应的 erb文件中：


```
<%= render :partial => 'footer' %>
```
注意: 上面的调用中, 直接使用了 'footer', 而不是 `_footer.html.erb` .这也是Rails的惯例.

## 带参数的partial
如果，某个partial ,是需要参数的，（例如： 年份是个变量）


```
<!-- 下面这段是版权声明，多个页面都需要重用  -->
<footer>
  copyright@ <%= year %>xx.co.ltd
</footer>
```
那么，在调用时，就：

```
<%= render :partial => 'footer', :locals => {:year => 2016} %>
```

可以看到, 使用了 locals 来传递参数.

## 不要使用嵌套层次过多的 partial

例如 partial A 嵌套 partial B , partial B 嵌套 partial C. 一旦发展下去,
你就会发现自己的代码乱套了.

partial 失去了它简化代码的作用.

# helper 的用法

helper的目的是为了让view的代码看起来更简化.

它是一个ruby module, 可以定义在app/helpers 目录下的任何文件中. 例如:

```
# app/helpers/application_helper.rb
module ApplicationHelper
end
```

## 添加一个helper

```
module ApplicationHelper
  def say_hi name
    "hi #{name}"
  end
end
```

那么我就可以在 任意的view中,使用这个方法:

```
<%= hi "Jim" %>
```

我在任意的页面中,可以调用定义在任意helper中的方法. 例如:


```
# app/helpers/foo_helper.rb
module FooHelper
  def say_hi
    "hi"
  end
end
```

## 不要过多的使用helper ,

因为我们调用helper的时候,不会显式的写出caller. 例如:

```
# my_module 就是 say_hi 的caller
my_module.say_hi

# 下面,没有一个显式的caller, 那么,我们就不知道这个find_me方法定义在哪里.
find_me
```

另外, ruby本身就是动态语言.(它的很多方法,都不知道声明在哪里)


```
<%= show_header %>

<%= book.show_header %>
```
可以看出,下面的 book 是一个非常明显的caller. 我们在实战中,最好把方法,都定义在 model, controller中.
(model中的方法定义,更多一些)


# 不用学的
其他的没讲到的，都不用学。实战中根本用不到. 或者只是增加了复杂性 . 例如：

```
render 'filename'
render xxx,  :collection ...
render xxx,  :as ...
render xxx,  :object ...
```

# 课程的作业
1. 创建一个rails应用.
2. 创建一个controller:  my_views_controller
3. 创建action: simplest_view, 访问后, 可以显示当前时间.
"当前时间是:  2016 Nov 16 .... " (提示: 使用Time.now方法即可)
4. 创建action: hello_from_action. 在该action中定义一个变量 @name,
然后,访问页面时, 可以显示 "xx, 你好!"
5. 创建action: show_footer. 在对应的视图中,调用一个名为 "_footer.html.erb"的partial.
该partial中的内容如:  "copyright@2016, by <你的名字>"  . "你的名字"是通过变量
传入到这个partial中的.
6. 创建action: show_a_list, 在对应的view中, 创建一个数组, 然后使用<ul>, <li>标签显示出来,
例如:

```
<ul>
  <li>Banana</li>
  <li>Apple</li>
  <li>Orange</li>
</ul>
```


- 显示当前时间
- 在controller中定义一个变量, 然后把它在view中显示出来.
- 定义一个partial, 然后在view中调用它.

# 本节示例代码

本节中的例子，对应的源代码，可以来这里下载： https://github.com/sg552/rails_lesson_3_view

本节对应的ppt, 可以来这里下载： http://siwei.me/about-me/speaks


# todo : raw
