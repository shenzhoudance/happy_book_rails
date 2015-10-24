Rails视图
=========
>Rails中视图是一个erb模板,即含有嵌入式的Ruby代码的HTML.他的作用是与控制器进行交互,既可以接收控制器中的操作请求,也可以把请求返回给控制器.

视图渲染
--------

视图渲染流程:当访问localhost:xxxx/books/这个网址的时候,它先去查看路由文件(routes.rb)是否有books这个路由(resources:books),再去查看是否有BooksController,最后它会自动的去app/views/books/
下渲染视图

控制器响应
----------

>HTTP响应的三种方法:
*   render方法
*   redirect_to方法
*   head方法

###render方法

```ruby
def update
  #渲染同个控制器中的其他动作模板
  render "edit"
  render "edit.html.erb"
  #除了字符串,还可以用Symbol
  render :edit
  render action: :edit
  #渲染其他控制器的动作模板
  render "porducts/edit"
  render template: "products/edit"
  #渲染Json
  render json: @product

  除了以上这些,render方法还可以渲染之外的文件,文本,HTML,Javascript等,由于不常用,
  在这里不予研究
end
```

render方法的可选选项
```ruby
def update
  #layout
  render layout: "application"
  render layout => "application"

  还有content_type(渲染类型),location(HTTP Location报头),status(状态码)三个选项,由于不常用,这里不予研究
end
```
###redirect_to方法
```ruby
def update
  #重定向到product控制器的index动作
  redirect_to product_url
  ##返回前一个画面
  redirect_to :back
end
```
###两者区别

render是在构建时渲染哪个视图和其他静态资源,而redirect_to是停止代码运行,重新发送一个新的请求

###head方法(不常用)


布局与模板
----------

###静态资源标签
*  auto_discovery_link_tag
*  javascript_include_tag
*  stylesheet_link_tag
*  image_tag
*  video_tag
*  audio_tag

Rails中写法
```ruby
#js文件
<%=javascript_include_tag 'application' %>
#css文件
<%= stylesheet_link_tag "main" %>
#图片
<%= image_tag "main.png" %>
```
生成的script标签
```javascript
<script src='/assets/application.js'></script>
<link rel="stylesheet" type="text/css" href="<%= asset_path "main.css"  %>">
<image src="main.png">
```

###yield&content_for

```html
#这是一个布局
<html>
<head>
<%= yield :head %>
</head>
<body>
<%= yield %>
</body>
</html>
```

当你在controller中引用这个layout的时候,你写的内容在渲染时会插入'<%= yield %>'中,而用
```html
<% content for :head do %>
内容
<% end %>
```
会插入到对应的名为head的yield中

###局部视图

当你在页面中某一块,比如目录栏是固定的时候,就可以将这一部分提取出来,写在一个erb文件中,注意局部视图文件名的开头一定要是下划线`(_list.html.erb)`,
然后只需要在插入的地方加上
```html
<%= render partical => "list" %>
```

Q&A
---
Q:如何多个视图共用一个模板,他们可能只需要模板的一部分?

A:用```html<% if @is_show_top %> <% end %>``` 方法对该部分进行判断,默认该部分是显示的,需要在controller中对该变量赋值来确定是否需要,
```ruby
def show
  #该部分显示
  is_show_top = true
end
```
