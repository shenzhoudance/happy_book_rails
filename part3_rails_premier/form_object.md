#  form 与 表单对象。

前方高能：

1. 本节是rails三大难之一。比较烧脑。大家要务必作作业。不动手，学不会。
2. 关于提到的各种helper, 多看官方文档：

- 英文： http://guides.rubyonrails.org/form_helpers.html
- 中文： http://guides.ruby-china.org/form_helpers.html
- 也可以看API中的文档（写的也特别细） http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for

## 军规

老老实实的使用 `<%= form_for @book do |f| %>` 这样的标准形式( `@变量`, 不是`:变量`, 不要有奇葩的action, 不要让model 对应的class
对不上号)

初学者不要使用 `simple_form_for`, `nested_form` 这样的插件. 对于老手也不建议在商业 项目中使用.

## 目录：

1. 十年前的  form 表单，在后台是如何处理的。
2. 演进过程。
3. 表单对象的优缺点，好处

## 复习： 浏览器，传数据给服务器，能传几种数据类型？

三种.

1.字符串.例如:  `/some_url?a=123`

`a = '123'`

2.数组. 例如: `/some_url?a[]=1&a[]=2&a[]=3`

可以知道, `a = ['1', '2', '3']`

对于Rails的Controller, 可以处理为:

```
Started GET "/books?a[]=1&a[]=2&a[]=3" for 127.0.0.1 at 2016-10-06 15:48:10 +0800
Processing by BooksController#index as HTML
  Parameters: {"a"=>["1", "2", "3"]}
```
可以看到 Rails中, 也是一样处理的.

3.Hash. 例如:

`/some_url?student[name]=jim&student[age]=20`

`student = { name: "jim", age: "20" }`

对于Rails的日志:

```
Started GET "/books?student[name]=jim&student[age]=20" for 127.0.0.1 at 2016-10-06 15:48:52 +0800
Processing by BooksController#index as HTML
  Parameters: {"student"=>{"name"=>"jim", "age"=>"20"}}
```

以上形式, 对于所有类型的http request都使用.

如果我要发起一个POST 请求
```
POST    /ooxx.html      (form)
```
就要参数放到form 表单中。

```
<form action='ooxx.html' method=POST>
  <input name='a' value='1'/>
  <input name='b' value='1'/>
  ....
</form>
```

如果我有一个对象，叫  article(中文名： 帖子）,  它有两个属性：  `title`, `content`.

那么，我们在Rails中，约定， 对应的html表单，就应该写成：：

```
<form action='/articles'  method='post' >
  <input type='text' name='article[title]' value= '我是标题' />
  <input type='text' name='article[content]' value= '我是正文' />
</form>
```

这样, 我们传到服务器端的参数, 就是:

```
{
    article: {
        title: "我是标题",
        content: "我是正文"
    }
}
```

于是，就可以很方便的让我们后端的代码来处理。后端代码可以非常的简单的
把上面的hash转换成对应的对象。


rails是可以自动分辨, 某个参数的值，是：  字符串，数组， 还是hash
```
<form action='..' method='..'>
   <input type='text' name='article[title]' value='三体'/>        params[:article][:title]  => '三体'

   <input type='text' name='colors[]' value='green'/>
   <input type='text' name='colors[]' value='red'/>
   <input type='text' name='colors[]' value='yellow'/>
                                                     params[:colors]   => ['green', 'red', 'yellow']
   <input type='text' name='article[readers][]' value='jim'/>
   <input type='text' name='article[readers][]' value='lilei'/>
                                                     params[:article][:readers] => ["jim", "lilei"]

</form>
```

上面的form表单, 传递的参数, 实际上是一个大hash, 形如:

```
  {
     article: {
        title: '三体',
        colors: ['green', 'red', 'yellow'],
        readers : [
          "jim",
          "lilei"
        ],
     }
  }
```

(也可以在hash里面，放数组，再放hash, 再数组。 总之，可以各种混用。
但是： 实际工作中，不要这样用。会被同事骂。 也会把自己绕蒙）

# 于是,我们从十年前,比较原始的web开发时代开始说起.

对于下面的这个表单：
```
<form action='/articles'  method='post' >
  <input type='text' name='article[title]' value= '我是标题' />
  <input type='text' name='article[content]' value= '我是正文' />
</form>
```

它传给后端的值也就是:

```
{
    article: {
        title: "我是标题",
        content: "我是正文"
    }
}
```

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

## 问题就出现了: 一个form 表单有很多个参数怎么办?

对象的属性越多，传递过来的参数就越多，上面的赋值语句就越多。

我曾经见过 有20行语句， 都是： `request.getParameter('...')`

所以，解决方法：  使用表单对象 `form object`。来对这么多个参数进行封装.

最开始的表单对象： 需要手动创建一个object:


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

## 知识点: 使用ORM后,做数据库操作时, 有三个层次要参与:

1.  form object
2.  ORM model
3.  数据库table

以上面的  `article` 为例子。  有两个属性：  `title`, `content`.

纯HTML   |  form object  |  ORM model  |  DB TABLE
------- | ---- | ---- | ---

###  1. 纯html

rails是可以自动分辨, 某个参数的值，是：  字符串，数组， 还是hash
```
<form action='..' method='..'>
   <input type='text' name='article[title]'/>        params[:article][:title]
   <input type='content' name='article[content]'/>   params[:article][:content]
</form>
```

### 2. form object:

在rails中是隐形的。你看不到它的声明。因为：它是在运行时，被rails中的某些方法动态创建的。

p.s. 动态创建方法的例子（javascript)

```
my_string = 'function hi(){  console.info("hi") }'
"function hi(){  console.info("hi") }"
eval(my_string)
undefined
hi()  # =
```

(在其他语言和框架中，这个对象，都是 显式 声明的（你的手写出来）, 在struts中就要这样）

rails中， 动态创建的form object, 理论上是这样：
(form 中包含什么参数, 或者说数据库中有多少列, 就有多少attribute)

```
class Article
  attr_accessor :title
  attr_accessor :content
end
```

# 通过 form_for 来使用表单对象.

所以，rails中， form 就要这样写：

```
<% form_for @aritcle do |f| %>
  <%= f.text_field :title %>
  <%= f.text_field :content %>
<% end %>

```
上面的重点，在于：  `do ... end`.  它说明了rails 是如何调用 上面的隐形的表单对象的。


这一句:

```
  <%= f.text_field :title %>
```

就是调用了 上面的 `Article`的 `attr_accessor :title`.

如果说`article` 是个表单对象的话,

- 编辑 article的时候， 要显示原来的值， 就是： `article.get_title`
- 保存 article的时候， 要保存传过来的值，就是：  `article.title=`

在上面的 `do |f| ... end`中, 这个`f` 就是表单对象.

`f.text_field :title` 不但会生成一个`<input type='text' />`标签,而且还会通过调用表单对象的 `.get_title` 方法,
来为这个`<input/>`标签设置初始值.

### 3. ORM model.

app/models 目录下的article.rb：
```
class Article < ActiveRecord::Base
  # rails会自动生成：  title, content 的 accessor
end
```

(也可以认为， 在Rails中， ORM model 跟 form object model 是一个文件。

### 4. DB table:

|articles表:|
|--|
|第一个列：title|
|第二个列：content|


## 表单对象与持久层,在Rails中是一个.

1. 表单对象（处理表单代码时, 把参数保存到 对象中）
2. 持久层（把对象中的数据保存到DB）

在rails中这两个是一样的东东。

## 使用Hash为Model赋值.

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

### 演变1： (一个属性一个属性的赋值)

该写法常见于： 从java转行过来的rails程序员。

```ruby
article = Article.new({
    :title => params['article']['title'],
    :content => params['article']['content']
})
article.save  # 在这里执行  insert into articles values (...)
```

### 演变2：(直接给构造函数一个hash , 再save)

```
article = Article.new params['article']
article.save
```

### 演变3：(把 `new ... save` 的步骤, 省略成: `create`)

```
Article.create params['article']
```


## 引申： java 与 ruby的不同（  语言能力上的，特别是元编程的不同）

# 对于持久层的model 声明:

我们假设`articles` 表， 有两个列：  `title`, `content`:

```
class Article < ActiveRecord::Base
end
```

它就会自动出现下面的方法：

```
article = Article.create ...
article.title   # => 获得title
article.content # => 获得content
```

java代码则是任何方法都要手写：

```
public Article extends ...{
   private String title;
   public String getTitle() { ...}  // 需要手动定义
   public void setTitle() { ... }
}
```

# TODO 送给好奇宝宝
# 元编程动态声明方法的例子：

可以看出ruby元编程极其简单.

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

## form_for

下面是一个最常见的Rails表单:

```
<%= form_for @user do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.submit %>
<% end %>
```

`form_for` 是个方法。
要三个参数：

```
form_for(record, options = {}, &block)
```


对于下面的代码:
```
<%= form_for @user do |f| %>
```

`record`:是 `@user`,
`options`, 就是 `{}`, 所以被省略掉了。
最后一个参数，是 `block`.

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

这个`f`, 就是block中的参数，也可以直接把它看成： `form object`. (表单对象)

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

例如, 比较下面两种写法:

1.form的rails写法:
```
<%= form_for @post, html: { class: "edit_post", id: "edit_post_45" } do |f| %>
  <%= f.text :title, '我是标题' %>
<% end %>
```
2.form的HTML写法:
```
<form action='/posts'  method='post' class='edit_post' id='edit_post_45' >
  <input type='text' name='post[title]' value= '我是标题' />
</form>
```

我们发现两者差别不大.

但是, 在做 编辑某个记录的时候, 我需要:
1. 生成一个form object
2. 生成的html 标签,带有默认值.

(下面就是php风格：)
```
<form action=.. >
  <input type='text' name='post[title]' value=<%= @post.title %> />
  <input type='text' name='post[content]' value=<%= @post.content %> />
</form>
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

TODO: 把臃肿的代码，COPY到这里。或者做个截图.

## form_for 很智能的地方。

过程:  rails发现了 `form_for` 的唯一参数: @post, 它就会开始按照"约定" 来猜测
各种参数: `form_object`, `method`, `action` ...

所以,对于 新建操作的form:

```
<form action='/articles' method = 'post' >
</form>
```

和对于 编辑操作的form:
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


## 使用form object 生成输入项的默认值.

text_field,  select
如何保证在编辑某个属性的时候, 它能选中了某个默认值?
```
<select>
  <option name=...> value</option>
  <option name=... <%=if @post.title == 'xx'  %> selected <% end %>> value</option>
  <option name=... selected> value</option>
  <option name=...> value</option>
</select>
```

在rails中,可以使用 `select`的辅助方法.

```
<%= select_tag options_for_select([1,2,3], default_value ) %>
```


作业：

1. 新建一个项目, 就是对学生的增删改查.
2. 实现一个form ,  form中，包含：
  2.1 姓名 string
  2.2 性别 单选 radio  , 选项: male, female
  2.3 爱好 多选 checkbox, 选项: lol, dota, cf, music
  2.4 自我评价 textarea
  2.5 星座 ( select)
  ( 把上面几个学好就可以。 不用学的：   日期输入框,数字， url, phone .. 其他的都不用学。)

要使用form_for

TODO 下面作业是下节课的

3. 把new.html.erb和 edit.html.erb中的 form_for 提取成为一个partial.

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


