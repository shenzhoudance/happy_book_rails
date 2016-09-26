Rails视图
=========

只学哪些内容：
1. erb文件。 （基本功)

<% a = [1,2,3] %>

<%= a %>

2. 知道 partial .

<html>
  <head>
    <style>....</style>
    <script>...</script>
  </head>

  <!-- 下面是正常的erb代码 -->
  <%= @books %>
</html>

partial: 就是一段可以重用的 erb代码。

步骤：
1. <%= render :partial => 'my_partial' %>

2. 新建一个文件：  app/views/books/_my_partial.html.erb

3. 把你要提取出来的内容，防到这个文件中。

作用： 把页面精简！

## 渲染页面的几种形式

1. html ( erb)  默认渲染这个形式

## 布局

所有 erb 文件， 都会渲染在 layout中。
Rails 默认的layout文件：  application.html.erb


# @变量：  把变量在controller中声明， 然后就可以在 view中调用了。

哪些不学。

1. format 这个东东不用学。
2.
