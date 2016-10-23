# form helpers 表单辅助方法

本节, 文档: http://guides.rubyonrails.org/form_helpers.html

可以看API:  http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html

注:

前提,我们已经定义好了 `class Article`, 形如:

```
class Article < ActiveRecord::Base
end
```

表 `articles` 有个列: title

假设 @article 是 Article 的一个实例.

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

## 其他 helper

可以查看官方文档. 以及我们的demo
