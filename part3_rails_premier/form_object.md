1. 为什么有时候是 :var,  有时候是 @var
由 api 决定的。  以及  rails  的省略手法决定的。


get :index ,  :name => 'dashi'

params[:name]
params['name']
params["name"]

<%=  form_for @name  %>
<%=  form_for :name  %>
<%=  form_for 'name' %>

"name",  'name',  :name
string ,  string,  symbol.

symbol:  不变的字符串。  ruby 的概念。
"name",  'name',  :name


<%= form_for @post do |f| %>
  ...
<% end %>
is then equivalent to something like:

<%= form_for @post, as: :post, url: post_path(@post), method: :patch, html: { class: "edit_post", id: "edit_post_45" } do |f| %>
  ...
<% end %>

<form action='/posts'  method='post' >
  <input type='text' name='post[title]' value= '我是标题' />
  <input type='text' name='post[content]' value= '我是正文' />
</form>


RESTful   POST: create,  PUT/PATCH： update, GET:  index, show, new, edit, DELETE: destroy
表单对象  form-object?
http 参数 和请求方式
  GET     ooxx.html?a=1&b=2&ids[]=3&ids[]=4&post[title]='some_title'&post[content]='some_content'
    # ids = [3,4]
    # post =  { 'title':'some_title', content: 'some_content'  }
  POST    ooxx.html      (form)

如果我有一个对象，叫  post,  它有两个属性：  title, content.

# 10年前 de java代码：
title = request.getParameter('post["title"]')
content = request.getParameter('post["content"]')
Post post = new Post()
post.setTitle(title)
post.setContent(content)
post.save()

# 问题就出现了:  属性越多， 上面的赋值语句就越多。
所以，解决方法：  使用表单对象。

/form/  用来出现再 form/ controller 中， 代表表单对象。
class Post
  string title
  text content
end


/model  用来操作数据库的。 (java struts框架的里面的 臭名昭著的东东, 2005年)
class Post
  string title
  text content
end

Rails形式： 它的宗旨就是 方便程序员， 对人友好：
演变1：
post = Post.new :title => params['post']['title'],
  :content => params['post']['content']
post.save

演变2：
post = Post.new params['post']
post.save

演变3：
Post.create params['post']


引申： java 与 ruby的不同（  语言能力上的，特别是员编程的不同）
# model: 声明:  ( posts 表， 有两个列：  title, content)
class Post < ActiveRecord::Base
end

post = Post.create ...
post.title  # => 'ooxx'
post.content # => 'ooxx'

// java代码：
public Post extends ...{
   public String getTitle() { ...}
   public void setTitle() { ... }
}

元编程动态声明方法的例子：
class Post
end

post = Post.new
post.instance_eval("
                   def say_hi
                     puts 'hihihi'
                   end
                   ")
post.say_hi

# => 'hihihi'

那么问题来了：
为什么要用rails的自定义html 标签呢？  ( form_for, form_tag, text_field, select_tag)
答： 为了更加简单

比较:

<%= form_for @post, as: :post, url: post_path(@post), method: :patch, html: { class: "edit_post", id: "edit_post_45" } do |f| %>
  <%= f.text :title, '我是标题' %>
<% end %>

<form action='/posts'  method='post' >
  <input type='text' name='post[title]' value= '我是标题' />
  <input type='text' name='post[content]' value= '我是正文' />
</form>


看不出来.

但是, 在做 编辑某个记录的时候, 我需要:
1. 生成一个form
2. 生成的html 标签,带有默认值.

<input type='text' name='post[title]' value=<%= @post.title %> />
<input type='text' name='post[content]' value=<%= @post.content %> />

<%= form_for @post do |f| %>
  <%= f.text :title %>
<% end %>

过程:  rails发现了 form_for 的唯一参数: @post, 它就会开始按照"约定" 来猜测
各种参数: form_object, method, action ...

所以,对于 新建:
<form action='/posts' method = 'post' >
</form>

对于 编辑:
<form action='/posts/3/edit' method = 'put' >
</form>

变成ruby代码的话就是:
<% if @post.id.present? %>
  <form action='/posts/3/edit' method = 'put' >
<% else %>
  <form action='/posts' method = 'post' >

于是,我们就可以用:
<% form_for @post %>

而且, 每一个 <form> 中,都有一个authentity_token. 防止注入攻击.

比如说,我在购物时,需要提交表单, 付费 . 这个表单当中,很可能就是:
<form ... >
  <input name='price' value='1000'/>
</form>

这个form 是非常好伪造的. 所以,需要用authentity_token 来识别.

<form ... >
  <input type='hidden' name='authentity_token' value='a1b2c3d4.....' />
  <input name='price' value='1000'/>
</form>

authentity_token 是由服务器端(rails)生成的.  它针对不同的browser/session 生成不同的token .
于是我们就可以在服务器端做验证了.

class PostsController ...
  protec_from_forgery  # 这行代码的作用: 对每个form 做验证,看里面的token 是否跟服务器端匹配.
end



<% if @post.id.present? %>
  <form action='/posts/3/edit' method = 'put' >
<% else %>
  <form action='/posts' method = 'post' >
<% end %>
     <input type='hidden' name='token' value='<%= generate_token %>' />

如果使用了form tag:
<%= form_for @post do |f| %>

同理: text_field,  select

如何保证在编辑某个属性的时候, 它能选中了某个默认值?
<select>
  <option name=...> value</option>
  <option name=... <%=if @post.title == 'xx'  %> selected <% end %>> value</option>
  <option name=... selected> value</option>
  <option name=...> value</option>
</select>

<%= select_tag options_for_select([1,2,3], default_value ) %>



========
Rails中的各种 _path, _url

如何编辑某个user?

"/users/1/edit"

写成ruby代码:  "/users/" + user.id + "/edit"
写成ruby代码:  "/users/#{user.id}/edit"  # string interpolation 插入插值
<%= edit_user_url({:id => User.first.id}) %> <br/>
<%= edit_user_url {:id => User.first.id} %> <br/>
<%= edit_user_url :id => User.first.id %> <br/>
<%= edit_user_url :id => User.first %> <br/>
# 所以,一个默认的model  当它 to_string的时候, 是要返回 id 的.
<%= edit_user_url User.first %> <br/>


==== 有空使劲看:  http://guides.rubyonrails.org/routing.html
有 各种  _url,  _path 的来历和用法.
