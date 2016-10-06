#  form 与 表单对象。

提示：
1. 本节内容比较多。比较烧脑。大家要务必作作业。不动手，学不会。
2. 多看官方文档：
英文： http://guides.rubyonrails.org/form_helpers.html
中文： http://guides.ruby-china.org/form_helpers.html
也可以看API中的文档（写的也特别细）
http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for

目录：
1. 十年前的  form 表单，在后台是如何处理的。
2. 演进过程。
3. 表单对象的优缺点，好处

复习： 浏览器，传数据给服务器，能传几种数据类型？

1. /some_url?a=123
 字符串： a = '123'

2. /some_url?a[]=1&a[]=2&a[]=3
数组： a = ['1', '2', '3']

Started GET "/books?a[]=1&a[]=2&a[]=3" for 127.0.0.1 at 2016-10-06 15:48:10 +0800
Processing by BooksController#index as HTML
  Parameters: {"a"=>["1", "2", "3"]}


3. /some_url?student[name]=jim&student[age]=20
hash:  student = { name: "jim", age: "20" }

Started GET "/books?student[name]=jim&student[age]=20" for 127.0.0.1 at 2016-10-06 15:48:52 +0800
Processing by BooksController#index as HTML
  Parameters: {"student"=>{"name"=>"jim", "age"=>"20"}}


TODO: 把下面的放到文章前面。
### 表单对象  form-object?


## http 参数 和请求方式

get请求： 把参数放到url中。参数可以有不同的形式。
又可以是数组，又可以是普通的字符串，又可以是一个hash.
(当然，里面所有的值，都是 String)

<a href='ooxx.html?a=1&b=2&ids[]=3&ids[]=4&post[title]='some_title'&post[content]='some_content''>...</a>
GET     ooxx.html?a=1&b=2&ids[]=3&ids[]=4&post[title]='some_title'&post[content]='some_content'
  # a = "1", b = "2" ( 普通参数）
  # ids = ["3","4"]  (数组）
  # hash 对象参数
  # post =  { 'title':'some_title', content: 'some_content'  }

如果我要发起一个POST 请求
POST    ooxx.html      (form)
把参数放到表单中。

<form action='ooxx.html' method=POST>
  <input name='a' value='1'/>
  <input name='b' value='1'/>
  ....
</form>

如果我有一个对象，叫  article(中文名： 帖子）,  它有两个属性：  title, content.

那么，我们在Rails中，约定， 对应的html表单，就应该写成：：
```
<form action='/articles'  method='post' >
  <input type='text' name='article[title]' value= '我是标题' />
  <input type='text' name='article[content]' value= '我是正文' />
</form>
```
{
    article: {
        title: "我是标题",
        content: "我是正文"
    }
}

这样，就可以很方便的让我们后端的代码来处理。后端代码可以非常的简单的
把上面的hash转换成对应的对象。


rails是可以自动分辨, 某个参数的值，是：  字符串，数组， 还是hash
<form action='..' method='..'>
   <input type='text' name='article[title]'/>        params[:article][:title]
   <input type='content' name='article[content]'/>   params[:article][:content]

   <input type='text' name='colors[]' value='green'/>
   <input type='text' name='colors[]' value='red'/>
   <input type='text' name='colors[]' value='yellow'/>
                                                     params[:colors]   => ['green', 'red', 'yellow']
   <input type='text' name='article[readers][]' value='jim'/>
   <input type='text' name='article[readers][]' value='lilei'/>
                                                     params[:article][:readers] => ["jim", "lilei"]

  {
     article: {
        readers : [
          "jim",
          "lilei"
        ],
     }
  }

</form>

(也可以在hash里面，放数组，再放hash, 再数组。 总之，可以各种混用。
但是： 实际工作中，不要这样用。会被同事骂。 也会把自己绕蒙）
实战时： 就用最基本的3种形式就可以。

TODO 再往前面放
# 10年前 de java代码：

对于下面的这个表单：
```
<form action='/articles'  method='post' >
  <input type='text' name='article[title]' value= '我是标题' />
  <input type='text' name='article[content]' value= '我是正文' />
</form>
```
{
    article: {
        title: "我是标题",
        content: "我是正文"
    }
}

10 年前的java代码，是如何处理的呢？
```java
// request 是 一个内置的对象
// step1. 获取浏览器传过来的所有参数
String title = request.getParameter('article["title"]');
String content = request.getParameter('article["content"]');

// step2. 初始化一个 model.  并且 设置它的各种值
Article article = new Article();
article.setTitle(title);
article.setContent(content);

// step3. 保存
article.save();
```

## 问题就出现了:  对象的属性越多，传递过来的参数就越多，上面的赋值语句就越多。
我曾经见过 有20行语句， 都是： request.getParameter('...')

所以，解决方法：  使用表单对象。

最开始的表单对象： 需要手动创建一个object:

####  注释掉。 /form/  用来出现再 form/ controller 中， 代表表单对象。
##### 这个model  用来操作数据库的。

(java struts框架的里面的 臭名昭著的东东, 2005年)
```

class Article {
  private String title;
  private String content;

  //getter, setter...

}

```

所以这就尴尬了：  表单对象，跟数据库的ORM对象，一模一样。导致了同样的代码出现在了 两个文件当中。

## 插一嘴：  做ORM操作时， 内心要知道3个东东：

1.  form object
2.  ORM model
3.  数据库table

以上面的  article 为例子。  有两个属性：  title, content.

纯HTML   |  form object  |  ORM model  |  DB TABLE
------- | ---- | ---- | ---
1. 纯html
rails是可以自动分辨, 某个参数的值，是：  字符串，数组， 还是hash
<form action='..' method='..'>
   <input type='text' name='article[title]'/>        params[:article][:title]
   <input type='content' name='article[content]'/>   params[:article][:content]
</form>

2. form object:  在rails中是隐形的。你看不到它的声明。因为：它是在运行时，被
rails中的某些方法动态创建的。

p.s. 动态创建方法的例子（javascript)

```
my_string = 'function hi(){  console.info("hi") }'
"function hi(){  console.info("hi") }"
eval(my_string)
undefined
hi()  # =
```

(在其他语言和框架中，这个对象，都是 显式 声明的（你的手写出来）, 在struts中就要这样）

rails中， 动态创建的form object, 看起来是这样：

```
class Article
  attr_accessor :title
  attr_accessor :content
end
```

所以，rails中， form 就要这样写：

```
<% form_for @aritcle  do  |f| %>
  <%= f.text_field :title %>
  <%= f.text_field :content %>
<% end %>

```
上面的重点，在于：  do ... end.  可以看出， rails 是如何调用 上面的隐形的表单对象的。

  <%= f.text_field :title %>

就是调用了 上面的  attr_accessor :title,
content 同理。

 编辑 article的时候， 要显示原来的值， 就是： article.get_title
 保存 article的时候， 要保存传过来的值，就是：  article.title=


3. ORM model.

app/models 目录下的article.rb：
```
class Article < ActiveRecord::Base
  # rails会自动生成：  title, content 的 accessor
end
```

(也可以认为， 在Rails中， ORM model 跟 form object model 是一个文件。
4. DB table:

articles:
第一个列：  title
第二个列：  content


1. 表单对象（处理表单代码时, 把参数保存到 对象中）
2. 持久层（把对象中的数据保存到DB）

在rails中这两个是一样的东东。

##
Rails形式： 它的宗旨就是 方便程序员， 对人友好：

回到刚才的java代码： 可以看到，给对象的赋值是一个一个来的：

// 这个就是form 提交过来的 参数：
{
    article: {
        title: "我是标题",
        content: "我是正文"
    }
}

```java
String title = request.getParameter('article["title"]');
String content = request.getParameter('article["content"]');
Article article = new Article();
article.setTitle(title);
article.setContent(content);
article.save();
```

演变1： (一个属性一个属性的赋值) 该写法常见于： 从java转行过来的rails程序员。

```ruby
article = Article.new({
    :title => params['article']['title'],
    :content => params['article']['content']
})
article.save  # 在这里执行  insert into articles values (...)
```

演变2：(直接给构造函数一个hash , 再save)

```
article = Article.new params['article']
article.save
```

演变3：(把 new ... save 的步骤, 省略成: create )

```
Article.create params['article']
```


引申： java 与 ruby的不同（  语言能力上的，特别是元编程的不同）

# model: 声明:  ( articles 表， 有两个列：  title, content)

```
class Article < ActiveRecord::Base
end
```

它就会自动出现下面的方法：

```
article = Article.create ...
article.title  # => 'ooxx'
article.content # => 'ooxx'
```

// java代码：

```
public Article extends ...{
   private String title;
   public String getTitle() { ...}  // 需要手动定义
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
<%=  form_for @name  %>   写成这样。  下面两种，不推荐。除非你特别有把握。
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
<form action='/articles' method = 'post' >
</form>
```

对于 编辑:
```
<form action='/articles/3/edit' method = 'post' >
  <input type='hidden' name='_method' value='put'/>
</form>
```

变成ruby代码的话就是:

```
<% if @article.id.present? %>
  <form action='/articles/3/edit' method = 'put' >
<% else %>
  <form action='/articles' method = 'post' >
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


作业：

1. 实现一个form
2. form中，包含：

<input type='text'/>
<input type='hidden'/>
<textarea/>
<select>
<options>
<input type='radio'/>
<input type='checkbox'/>
<input type='file'/>
<input type='password'/>

把上面几个学好就可以。

不用学的：   日期输入框,数字， url, phone .. 其他的都不用学。

3. 实现一个 select （ 选择框，下拉单， drop down (US 叫法））
里面的选项， 来自于： 数据库。  举个例子：


3.1
数据库中，有两个表，books(书） , 一个是： publisher （出版社）   books :  publisher = n : 1

books表， 有下面的列：

id    title    publisher_id
-- |  -- |  --
1  |   java入门  |
2  |   ruby 入门  |
3  |   javascript 入门 |

publisher 表只有两个列：

id,   name
--- | ---
10  | 人民教育出版社
20  | 商务印书馆
30  | 电子工业出版社

3.2
写出下面的 html: (使用  select_tag  与 options_from_collection_for_select )

```
选择出版社：
<select name='books[publisher_id]' >
  <option value='10'>人民教育出版社</option>
  <option value='20'>商务印书馆</option>
  <option value='30'>电子工业出版社</option>
</select>
```

3.3
在新建或者 编辑 books的页面中，  可以对publisher 进行选择。

并且，可以在后台(create, update action中）保存。



再进一步的细化提示：
   1. 创建两个表。 (rails generate migration )
   2. 有相关的 model ( app/models/book.rb,  publisher.rb )
   3. 查看(实现形式1）： select_tag, options_from_collection_for_select
   4. 查看(实现形式2）： select_tag , options_for_select


## FormHelper FormTagHelper.

两个例子，来对比说明。


区别:

1. form helper:

  <%= f.text_field :title %>

  1.1. 需要与 form object ( <%= form_for @book do |f| %>  .. .<% end %> )配合使用。
  1.2  它的name 是自动生成的。 例如：  name="student[age]"
      student 必须是 某个 class的  instance
      并且， age 必须是form object的方法（也就是 数据库的列。)
  优点： 可以简化我们对表单项的操作。（例如： 下拉单 或 文本框的 默认值）

2. form tag helper:
  <%= text_field_tag 'my_title' %>

  2.1 可以独立使用。 跟表单对象无关。
  2.2 名字可以随意取。 name='abc'
  优点： 特别灵活。  可以脱离表单对象使用。  而且 便于理解。

相同点：   作用是一样的。

例如：

  <%= f.submit "OK"%>
  <%= submit_tag "OK" %>

都会生成：
<input type="submit" name="commit" value="OK">

例子2：

  <%= f.text_field :title %>
  <%= text_field_tag 'article[title]' %>

都会生成：

  <input type="text" name="article[title]" id="article_title">


所以说， form helper 与 form tag helper 都是一样的。 一个东西。写法不同。

甚至， 我们在API文档上，都可以看到Rails 作者，告诉我们：

form helper: http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html
form tag helper: http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html

中，都可以看到， xx_field 的文档中，会说： 请参考  xx_field_tag . 例如：

time_field 中：
```
Options: Accepts same options as time_field_tag
```

提示： 我们做rails的前半年，一定要多翻： 上面两个文档。

## 注意：

api 的文档， 跟真正的用法，有一定区别。
http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-label

(前提： @article 是在 controller中定义好的）
下面，两个input， 是一样的。
<%= form_for @article do |f| %>
  title: <%= f.text_field :title %><br/>  # 大家都使用这个写法！
  <%= text_field :article, :title %>      # 忘掉这个写法。
end

都会生成： <input type="text" name="article[title]" id="article_title">

但是，实战当中， 我们都是用 f.text_field('method') 这样的 简写形式。
不会使用 text_field('object', 'method') 这样的形式

## 第二个提示（内容与上面的可能重复）
<%= form_for @article do %>

等同于：

两个形式：

1.(@article 是 Article.new的时候）
<%= form_for :article , :url => article_path, :method => 'post'  do %>

2. (@article 是 Article.find(2)的时候）
<%= form_for :article , :url => update_article_path(:id => 2), :method => 'put'  do %>

注意：

1. 忘掉   form_for :xx 的形式， 使用 form_for @xx .
2. 只要当前操作与 数据库有关系，那么就用  form_for ，会让你特别省力。  除非有必要，才
使用  form_tag
例如：

form_for  对应：  创建， 更新操作。
form_tag  对应：  某个列表页的搜索。



