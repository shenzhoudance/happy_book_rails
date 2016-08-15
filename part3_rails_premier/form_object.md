#  form 与 表单对象。

下面是一个最常见的表单:

```
<%= form_for @user do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.submit %>
<% end %>
```

form_for 是个方法。
要三个参数：

```
form_for(record, options = {}, &block)
```


对于下面的代码:
```
<%= form_for @user do |f| %>
```
record:  @user,    options, 就是 {}, 所以被省略掉了。
最后一个参数，是 block.

1. 为什么，第二个参数可以被省略掉？

因为， 在ruby当中， 规定： 如果一个函数的参数中，有block, 那么这个block,
必须是最后一个参数。

所以，ruby ，要解析一个函数的时候，一开始，就能知道， do ... end 之间，这是一个
block, 所以，它就是最后的参数。

而： form_for @user, 表示， form_for的第一个参数，就是 @user.

所以，根据form_for的定义：

1. 第一个参数，有了。
2. 第二个参数，在定义中，就是： 可选的。 ( options = {} )
3. 第三个参数：( &block ) 也有了。

所以这个函数就完整了，可以正常运行了。

下面的 `f.label`, `f.text_field`, `f.submit`, 中的 `f`:

```
<%= form_for @user do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.submit %>
<% end %>
```

这个f, 就是block中的参数，也可以直接把它看成叫： form object. (表单对象)

## 预习 rails中的简写。

1. 为什么有时候是 :var,  有时候是 @var
由 api 决定的。  以及  rails  的省略手法决定的。

例如：
我们要访问某个 controller的某个action:

```
打开：  http://localhost:3000/users?name=dashi
```

如果我要获取到这个url 的参数：

在rails中，有三种方式：

```
params[:name]
params['name']
params["name"]
```

所以，form_for 的第一个参数，也可以写成下面的三种形式。
```
<%=  form_for @name  %>
<%=  form_for :name  %>
<%=  form_for 'name' %>
```

"name" |  'name' | :name
---
string |  string |  symbol

symbol:  不变的字符串。  ruby 的概念。

```
<%= form_for @post do |f| %>
  ...
<% end %>
```
就等同于：

```
<%= form_for @post,
    as: :post,
    url: post_path(@post),
    method: :patch,
    html: { class: "edit_post", id: "edit_post_45" }
    do |f| %>

    # 其他代码

<% end %>
```

问题来了：  上面的代码中， form_for 有多少个参数呢？

如果你看了API， 就知道了，它有3个参数。

但是，为什么， as, url, method, 都不是参数呢？ 因为，他们是一个hash,
在ruby中， hash最外层的大括号很多时候可以省略。

哪些时候可以省略呢？

该hash作为参数时，如果它不是最后一个非block参数。它就可以省略大括号。

```
form_for  @user,   { url: '', method: '' } do |f| ...
end
```




### 表单对象  form-object?

## http 参数 和请求方式

get请求： 把参数放到url中。
GET     ooxx.html?a=1&b=2&ids[]=3&ids[]=4&post[title]='some_title'&post[content]='some_content'
  # ids = [3,4]
  # post =  { 'title':'some_title', content: 'some_content'  }

POST    ooxx.html      (form)
把参数放到表单中。

如果我有一个对象，叫  post,  它有两个属性：  title, content.

对应的html表单，就应该写成：：
```
<form action='/posts'  method='post' >
  <input type='text' name='post[title]' value= '我是标题' />
  <input type='text' name='post[content]' value= '我是正文' />
</form>
```

# 10年前 de java代码：

```java
String title = request.getParameter('post["title"]');
String content = request.getParameter('post["content"]');
Post post = new Post();
post.setTitle(title);
post.setContent(content);
post.save();
```

## 问题就出现了:  属性越多， 上面的赋值语句就越多。

所以，解决方法：  使用表单对象。

最开始的表单对象： 需要手动创建一个object:

/form/  用来出现再 form/ controller 中， 代表表单对象。

这个model  用来操作数据库的。 (java struts框架的里面的 臭名昭著的东东, 2005年)
```

class Post {
  private String title;
  private String content;

  //getter, setter...

}

```

所以这就尴尬了：  表单对象，跟数据库的对象，一模一样。口可口可。

Rails形式： 它的宗旨就是 方便程序员， 对人友好：

演变1： (一个属性一个属性的赋值)

```ruby
post = Post.new :title => params['post']['title'],
  :content => params['post']['content']
post.save
```

演变2：(直接给构造函数一个hash , 再save)

```
post = Post.new params['post']
post.save
```

演变3：(把 new ... save 的步骤, 省略成: create )

```
Post.create params['post']
```


引申： java 与 ruby的不同（  语言能力上的，特别是员编程的不同）

# model: 声明:  ( posts 表， 有两个列：  title, content)

```
class Post < ActiveRecord::Base
end
```

它就会自动出现下面的方法：

```
post = Post.create ...
post.title  # => 'ooxx'
post.content # => 'ooxx'
```

// java代码：

```
public Post extends ...{
   public String getTitle() { ...}
   public void setTitle() { ... }
}
```

元编程动态声明方法的例子：

```
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

```

## 那么问题来了：

为什么要用rails的自定义html 标签呢？ , 例如:
```
form_for,
form_tag,
text_field,
select_tag
```

而不是:
```
<form>  , <input> ...
```

答： 为了更加简单

比较:

```
<%= form_for @post, html: { class: "edit_post", id: "edit_post_45" } do |f| %>
  <%= f.text :title, '我是标题' %>
<% end %>
```

```
<form action='/posts'  method='post' class='edit_post' id='edit_post_45' >
  <input type='text' name='post[title]' value= '我是标题' />
  <input type='text' name='post[content]' value= '我是正文' />
</form>
```

看不出来.

但是, 在做 编辑某个记录的时候, 我需要:
1. 生成一个form object
2. 生成的html 标签,带有默认值.

(下面就是php风格：)
```
<input type='text' name='post[title]' value=<%= @post.title %> />
<input type='text' name='post[content]' value=<%= @post.content %> />
```

(下面就是ruby风格)
```
<%= form_for @post do |f| %>
  <%= f.text_field :title %>
<% end %>
```

所以，php的代码，看起来就是场 噩梦。

例如：某个下拉框，有100个选项. 就要搞100次循环。然后，还要判断默认值。
那个时候，就会觉得代码特别的臃肿。

TODO: 把臃肿的代码，COPY到这里。

## form_for 很智能的地方。

过程:  rails发现了 form_for 的唯一参数: @post, 它就会开始按照"约定" 来猜测
各种参数: form_object, method, action ...

所以,对于 新建:

```
<form action='/posts' method = 'post' >
</form>
```

对于 编辑:
```
<form action='/posts/3/edit' method = 'put' >
</form>
```

变成ruby代码的话就是:

```
<% if @post.id.present? %>
  <form action='/posts/3/edit' method = 'put' >
<% else %>
  <form action='/posts' method = 'post' >
<% end %>
```

于是,我们就可以用:

```
<%= form_for @post %>
```

这段代码, 来表示上面提到的两种场合(@post 是已经存在的记录,还是没有存在的记录).

而且, 每一个 `<form>` 中,都有一个authentity_token. 防止注入攻击(XSS).

比如说,我在购物时,需要提交表单, 付费 . 这个表单当中,很可能就是:
```
<form ... >
  <input name='price' value='1000'/>
</form>
```

这个form 是非常好伪造的. 所以,需要用authentity_token 来识别.

```
<form ... >
  <input type='hidden' name='authentity_token' value='a1b2c3d4.....' />
  <input name='price' value='1000'/>
</form>
```

authentity_token 是由服务器端(rails)生成的.  它针对不同的browser/session 生成不同的token .
于是我们就可以在服务器端做验证了.

```
class PostsController ...
  protec_from_forgery  # 这行代码的作用: 对每个form 做验证,看里面的token 是否跟服务器端匹配.
end
```



```
<% if @post.id.present? %>
  <form action='/posts/3/edit' method = 'put' >
<% else %>
  <form action='/posts' method = 'post' >
<% end %>
    <input type='hidden' name='token' value='<%= generate_token %>' />
```

所以使用`form tag` ， 就再也不用人肉写上面那行的代码了。
```
    <input type='hidden' name='token' value='<%= generate_token %>' />
```

如果使用了form tag, 就是这样:

```
<%= form_for @post do |f| %>
```

同理: text_field,  select

如何保证在编辑某个属性的时候, 它能选中了某个默认值?
```
<select>
  <option name=...> value</option>
  <option name=... <%=if @post.title == 'xx'  %> selected <% end %>> value</option>
  <option name=... selected> value</option>
  <option name=...> value</option>
</select>
```

```
<%= select_tag options_for_select([1,2,3], default_value ) %>
```

