# 最简单的rails 入门过程（当成PHP来学）

没有讲的：

1. 数据库。 (model,  hibernate)
2. 复杂的路由。( route, dispatch,  struts)


# Rails中的视图

Rails架构：  M - V - C
（几乎是所有经典web项目的架构）

Model: 操作数据库。
Controller: 把每个 http request 分发到对应的 Action( method)
来处理
View: 显示 HTML 页面。

所以，一个例子：

http://host/fruits/new
（上面的url, 如果是按照rails的约定惯例来看的话，就是：
new 一个 fruit.  (显示 新建fruit的页面）

所以，rails 会把这个请求，根据某个规则，发送给 action。

## 详细过程（3步）

1. 一个人，在浏览器端，输入了一个网址:
http://server.com/fruits/new ，回车。(这会产生
一个 http request )

2. http request 从浏览器，发送到服务器端(server.com)之后，
Rails就会 把这个请求交给 router 来处理。

(接下来的事儿，都发生在 服务器端, 见上面的小王例子。）

3. router, 根据配置文件： config/routes.rb 中的配置：

```
Rails.application.routes.draw do
  resources :fruits # 根据这个路由配置
end
```

把这个请求，分发到：  fruits controller 中的  new action.

4. new action 做一些 处理， 显示对应的 ERB(JSP, PHP也是一样的)

```
class FruitsController < ApplicationController
  def new
    # 1. 渲染 一段字符串
    #render :text => 'hihihi'
    # 2. 渲染 一个json
    #render  :json => {
    #  key: 'value',
    #  name: 'dashi',
    #  sex: 'male'
    #}
    # 3. 啥也不写，就渲染对应的
    #  app/views/fruits/new.html.erb
  end
end
```
5. 如果要渲染的是一个  erb 页面， 我们可以这样写：
(为什么PHP 很好入门？ 就是因为，PHP 上来就让你写这个）

```

你好阿。
<% [1,2,3].each do |e| %>
  <%= e %> <br/>
<% end %>

```
(为什么PHP 入了门之后，就发现 到处都是坑？ 就是因为PHP
中， 只有这个.同理， JSP， ASP 稍微好点，但是也是一样。）

6. 有时候，如果某个erb文件， 过于复杂了。 例如： 20行。
或者， 某些代码可以重用。

```
<!-- 下面这段是版权声明，多个页面都需要重用  -->
<footer>
  copyright@2016 xx.co.ltd
</footer>
```

那么就把它写成一个 partial (片段）(注意，文件名以 "_"开头)

app/views/fruits/_footer.html.erb

然后，我们就可以在对应的 erb文件中：

```

<%= render :partial => 'footer' %>
```

如果，这个partial ,是需要参数的，（例如： 年份是个变量）

```

<!-- 下面这段是版权声明，多个页面都需要重用  -->
<footer>
  copyright@ <%= year %>xx.co.ltd
</footer>

```

那么，在调用时，就：

```
<%= render :partial => 'footer', :locals => {:year => 2018} %>
```
上面的： partial/locals 是固定的语法，就这么写。

## 不用学的。

其他的没讲到的，都不用学。 例如：

render 'filename'
render xxx,  :collection ...
render xxx,  :as ...
render xxx,  :object ...


# 作业

1. 创建一个rails项目. 例如:  market

2. 这个项目中, 大家可以访问  /fruits/list

打开后, 页面应该显示下面三行内容:

```
香蕉
苹果
橘子
```

(提示: 在view中定义一个数组, 然后循环显示.)

3. 上传到 github上.
