# form helpers 表单辅助方法

## 军规

一定要熟悉好标准的HTML表单标签. 否则你一定会问出小白问题.

在rails的头一年, 我们一定要玩命看官方的文档:

form helper: http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html

form tag helper: http://api.rubyonrails.org/classes/ActionView/Helpers/FormTagHelper.html

## 本节文档.

本节, 文档: http://guides.rubyonrails.org/form_helpers.html

可以看API:  http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html


## form_for 的完整形式

```
<%= form_for @article do %>
```

等同于：

两个形式：

1.(@article 是 Article.new的时候）
```
<%= form_for :article , :url => article_path, :method => 'post'  do %>
```

2.(@article 是 Article.find(2)的时候）
```
<%= form_for :article , :url => update_article_path(:id => 2), :method => 'put'  do %>
```

注意：

1. 忘掉   `form_for :xx` 的形式， 使用 `form_for @xx` .
2. 只要当前操作与 数据库有关系，那么就用  `form_for` ，会让你特别省力。  除非有必要，才
使用  `form_tag`
例如：

`form_for`  对应：  创建， 更新操作。
`form_tag`  对应：  某个列表页的搜索。


## `form_for` 与 `form_tag` 的区别和联系

如果我们在controller中定义了:

```
@book = Book.new
```

那么,在view中， 下面两个是相同的：

```
<%= form_for @book do |f|%>
  <%= f.text_field :title %>
<% end %>

<%= form_tag '/books' do %>
  <%= text_field_tag 'book[title]', '' %>
<% end %>
```



### form 中的authentity_token （也叫csrf token)

每一个 `<form>` 中,都有一个`authentity_token`. 防止注入攻击(XSS).

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

`authentity_token` 是由服务器端(rails)生成的.  它针对不同的browser/session 生成不同的token .
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

如果使用了form tag, 就可以自动生成 csrf token了.:

```
<%= form_for @post do |f| %>
```

## 接下来演示的前提

我们已经定义好了 `class Article`, 形如:

```
class Article < ActiveRecord::Base
end
```

表 `articles` 有个列: title

假设 @article 是 Article 的一个实例.

## FormHelper FormTagHelper.

两个例子，来对比说明。

区别:

1.`form helper`:

```
  <%= f.text_field :title %>
```

1.1. 需要与 form object ( `<%= form_for @book do |f| %>  .. .<% end %>` )配合使用。
1.2  对于生成的`<input type='text' name="student[age]"/>`中,
  它的`name` 是自动生成的。 例如：  `name="student[age]"`
    `student` 必须是某个model的实例
    并且， `age` 必须是form object的方法（也就是 数据库的列。)

  优点： 可以简化我们对表单项的操作。（例如： 下拉单 或 文本框的 默认值）

2.`form tag helper`:
```
  <%= text_field_tag 'my_title' %>
```

  2.1 可以独立使用。 跟表单对象无关。
  2.2 名字可以随意取。 name='abc'
  优点： 特别灵活。  可以脱离表单对象使用。  而且 便于理解。

相同点：   作用是一样的。

例如：

```
  <%= f.submit "OK"%>
  <%= submit_tag "OK" %>
```

都会生成：
```
<input type="submit" name="commit" value="OK">
```

例子2：

```
  <%= f.text_field :title %>
  <%= text_field_tag 'article[title]' %>
```

都会生成：

```
  <input type="text" name="article[title]" id="article_title">
```

所以说， `form helper` 与 `form tag helper` 都是一样的。 一个东西。写法不同。

甚至， 我们在API文档上，都可以看到 xx_field 的文档中，会说： 请参考  xx_field_tag . 例如：

time_field 中：
```
Options: Accepts same options as time_field_tag
```

提示： 我们做rails的前半年，一定要多翻： 上面两个文档。

## 注意：

api 的文档， 跟真正的用法，有一定区别。
http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-label

api 中写着：

```
text_field(object, method, options={})
```

实际问题来了：
(前提： `@article` 是在 controller中定义好的）
下面，两个`input`， 是一样的。

```
<%= form_for @article do |f| %>
  title: <%= f.text_field :title %>
  <%= text_field :article, :title %>
end
```

都会生成：

```
<input type="text" name="article[title]" id="article_title">
```

但是，实战当中， 我们都是用 `f.text_field('method')` 这样的 简写形式。
不会使用 `text_field('object', 'method')` 这样的形式

API 那样写，是因为在API文档中没有上下文。 所以它那么表示的。（深层会有很多逻辑，很多
动态生成的代码，我们不考虑）
## text_field 的两种不同写法

API写法:

```
  <%= text_field :article, :title %>
```

form object中的写法:

```
  <%= f.text_field :title %>
```

两个都会生成一样的内容, 我们实战中 只用 `f.text_field` 这样的形式.

## text_field 与 text_field_tag

用法:

```
<%= form_for @article do |f| %>
  <%= f.text_field :title %>
  <%= text_field :article, :title %>
  <%= text_field_tag 'article[title]' %>
<% end %>
```
会生成:

```
<input type='text' name='article[title]' />
```

`f.text_field` 会自带默认值, 如果希望 `text_field_tag` 也有默认值的话,就要:

```
<%= text_field_tag 'article[title]', '三体' %>
```

## select , select_tag 与 options helper

```
<%= select_tag 'sex', options_for_select(['男', '女']) %>
```

会生成:
```
<select id="sex" name="sex">
	<option value="male">男</option>
	<option value="female">女</option>
</select>
```

也就是说, 上面的 `select_tag` 会生成:


```
<select id="sex" name="sex">
</select>
```

`options_for_select(['男', '女'])` 会生成:

```
<option value="male">男</option>
<option value="female">女</option>
```

### options_from_collection_for_select

如果这些options 来自于 数据库, 假设已经存在一个表`books`, 有两个列: `id`, `title`,

这个表的值是:

id | title
-- | --
1 | 三体1
2 | 三体2
3 | 三体3

于是,
```
<%= options_from_collection_for_select Book.all, :id, :title %>
```
会生成:

```
<option value="1">三体1</option>
<option value="2">三体2</option>
<option value="3">三体3</option>
```

### collection_select

我们也可以直接使用 `collection_select`, 替代`select` 与 `options_from_collection_for_select`.

```
# 假设 f 是 book的表单对象.
<%= f.collection_select :title, Book.all, :id, :title %>
```

会生成:

```
<select name="book[title]">
	<option value="1">三体1</option>
	<option value="2">三体2</option>
	<option value="3">三体3</option>
</select>
```

但是这种写法往往不够灵活, 所以我们用的不多.

## textarea, password 与 hidden_field

```
<%= text_area_tag(:message, "Hi, nice site", size: "24x6") %>
<%= password_field_tag(:password) %>
<%= hidden_field_tag(:parent_id, "5") %>
```
会依次生成:

```
<textarea id="message" name="message" cols="24" rows="6">Hi, nice site</textarea>
<input id="password" name="password" type="password" />
<input id="parent_id" name="parent_id" type="hidden" value="5" />
```

## checkbox 多选 与 radio button 单选

多选的例子:

```
<%= check_box_tag(:banana) %>
<%= label_tag(:banana, "我喜欢香蕉") %>
<%= check_box_tag(:orange) %>
<%= label_tag(:orange, "我喜欢橘子") %>
```

会依次生成:

```
<input id="banana" name="banana" type="checkbox" value="1" />
<label for="banana">我喜欢香蕉</label>
<input id="orange" name="orange" type="checkbox" value="1" />
<label for="orange">我喜欢橘子</label>
```

单选的例子:

```
<%= radio_button_tag(:sex, "male") %>
<%= label_tag(:sex_male, "男") %>
<%= radio_button_tag(:sex, "female") %>
<%= label_tag(:sex_female, "女") %>

```

会依次生成:

```
<input id="sex_male" name="sex" type="radio" value="male" />
<label for="sex_male">男</label>
<input id="sex_female" name="sex" type="radio" value="female" />
<label for="sex_female">女</label>
```

## 其他 helper

可以查看官方文档. 以及我们的demo

## 作业

1. 使用 form_tag 和其他自定义标签, 写出一个表单, 包含:


提示:  form_tag 跟model 无关

2. 使用 form_for 和其他自定义标签, 写出一个表单.

提示: 需要创建一个model, 然后使用form_for , f.text_field 这样的形式来生成.

- select
- options
- input type = text
- input type = hidden
- input type = password
- input type = file
- textarea
- checkbox
- radio
