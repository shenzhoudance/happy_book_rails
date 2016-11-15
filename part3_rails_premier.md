# 什么是Rails.   为什么学习Rails.

1. Rails 是Ruby 的 Web框架。

语言： java, ruby, python... 学习了之后，你们会发现， 做不了任何事儿。 只能写个算法。不能做任何事儿。

包括：

- 在网页上显示个表单
- 处理一个request请求
- 处理数据库
- 无法在桌面上，显示一个对话框。

想做的话，需要做大量的底层工作。（把一个请求，转换成XX类，再借助YY类力量，使用W方法）

所以，我们做任何事情，都需要使用框架。(Framework)
常见的框架有：

java:  Spring, Struts, Hiberate ...
PHP:  phpcake, ThinkPHP, ...
Ruby: Rails, Sinatra
Python: Django, Tornado
javascript: Angular, React, Vuejs..

有了框架，我们做起事情来，才会特别简单，得心应手。

在上面的 web开发框架中， Rails, 完胜 其他框架。 什么叫完胜呢？

1. 开发速度第一。 其他框架一个月，Rails： 1～2周。  包括 PHP .
2.

## 为什么PHP 不是WEB开发的 首选。

1. PHP 的代码，很罗嗦。跟java一样罗嗦。 导致了： 项目一大， php的代码量跟java 是一样的。
2. 也许 php 的代码的开发速度比java快，但是， 只要在java项目中，不使用java bean, 那么JSP跟PHP是一样的。
可以认为java的开发速度比php 慢，是因为java在开发时，要遵循的条条框框太多了。
3. 维护上： php的代码跟java 几乎一样，导致了： php 的代码比ruby 代码多的多（可以认为是4，5倍）。记得一个点：
代码越多， 维护起来就越困难。

当我们面对 几根头发的时候，可以很容易的理顺。 (Ruby > python)
当我们面对一个 线团的时候，就没法弄了。  （PHP ,java）

Ruby的代码量大约是 Python的 80%。
Ruby的代码量大约时 java的 25%, 或者更少。

所以，我们维护php/java代码的时候，会发现，我们需要在各种文件(class)中 跳来跳去。眼花缭乱。

PHP之所以在国内流行，还是因为国人英语差了些。 外面教的人很多。

## 框架层面的比较

一个优秀的框架，应该具备下面的特点：

### ORM (数据库持久层）

传统操作数据库的方式：

```
select * from apples where name like '%红%';
```

很容易就编程复杂的 SQL：

十年以前，我们经常会看到这样的SQL代码：

```
SELECT a.name, a.color, a.weight, a.height...
   ..................
   .............
   ...........   a.origin
   FROM Apple as a

   f.name, f.color, f.shape,
   ...............
   ...............

   INNER JOIN fruit as f

   ON a.fruit_id = f.id
   ORDER BY 'a.created_at, a.update_at'
```

更多例子： 见： https://www.google.co.kr/search?q=complex+sql&espv=2&biw=1920&bih=987&source=lnms&tbm=isch&sa=X&ved=0ahUKEwjL0dSc6ojPAhURtJQKHfwkAwIQ_AUIBigB#imgrc=r06AHuSVg2BJpM%3A

所以，是个项目， 就好多这样的SQL。 几乎处于无法维护的状态。
（所以，当年才会有DBA 这样的现在看起来比较奇怪的职位。十年以前 , Oracle DBA, 月薪1万多）

大家就想，如果数据库，能够以面向对象的形式来考虑，会不会好很多？

apples 表：

列名  |  属性
-- | --
name  |  String
color |  String

这就是一个 model: 有两个属性： name, color.

如果用ruby语言来表示的话：

```
class Apple

  # 显式定义了两个 属性(在ruby中，属性就是实例变量）
  @name
  @color

  def name=(new_name)
    @name = new_name
  end

  def name
    return @name
  end

  def color=(new_color)
    @color = new_color
  end

  def color
    return @color
  end
end

```

所以，大家就考虑，我对数据库的 操作：

```
insert into apples(name, color) values ('红苹果', '红色');
```

可以直接使用面向对象的观点来操作：

```
apple = Apple.new
apple.name = '红苹果'
apple.color = '红色'
apple.save
```

## 同理， 删除：

SQL;
```
delete from apples where id = 1;
```

面向对象的思想：

```
apple = Apple.where('id = 1');
apple.delete()
```

## update 也是一样。

## 搜索：

sql:
```
select * from apples;
```

面向对象：

```
Apple.all
```

复杂一些的搜索， SQL：

```
select * from apples where name like '%苹果%' order by name desc;
```

面向对象：

```
Apple.where("name like '%苹果%'").order('name desc')
```

所以，上面的复杂SQL：


```

SELECT a.name, a.color, a.weight, a.height...
   ..................
   .............
   ...........   a.origin
   FROM Apple as a

   f.name, f.color, f.shape,
   ...............
   ...............

   INNER JOIN fruit as f

   ON a.fruit_id = f.id
   ORDER BY 'a.created_at, a.update_at'
```

就可以写成：

```
Apple.join('fruits').order('apple.created_at')...
```

只不过，需要在apple.rb, fruit.rb对应的class中，进行配置：

```
class Apple
  belongs_to('fruit')
end

class Fruit
  has_many('apples')
end
```

总之，目的：

1. 我们知道，所有的sql语句，都可以使用 面向对象的方式，来操作。（用面向对象，封装 传统的数据库）
2. 难度系数永远是个定值。 (你操作数据库的难度和复杂度，不会随着对象的增加而失控）
（见恺恺的图片）


所以，我们一定要：

1. 掌握SQL的基础知识 (insert, delete, select. .. )
2. 然后才能使用 ORM （Object relation mapping)

学会了一个之后， 你看所有语言的ORM框架都是几乎一样的。

Rails中的 ORM 是最最简单的。


### 结语：

好的框架，必须有数据库持久层。

java: Hibernate  ( 一本砖头厚的书 300页，起。）
Rails: ActiveRecord ( 20页）


## 处理路由

### 铺垫
这个很重要。我在实际教学当中，发现的点。

我们做web开发， 记住： 页面间的跳转，是通过URL的变化来驱动的。
也就是说，打开一个URL， 就会显示一个页面。

注册页：  a.com/register.html
登录页：  a.com/login.html
处理登录页：  a.com/validate_login.html

（做前端开发 android/ios，或者桌面应用 LOL， QQ， 都是靠 事件来驱动的. 触发一个事件，就会显示一个页面。）

登录页：  输入完之后， 点击某个按钮，触发：   onclick 事件的 , 然后，程序内部判断。 判断完之后，跳转到 首页。
我在首页， 点击 某个按钮后， 触发 XX事件。

把握好这两点，两者开发的本质，就清楚了。

很多同学，如果还不清楚这两点， 就容易在很多知识点上，不理解。

所以，我们做web开发，一定要牢牢的知道， 哪个页面，对应哪个URL

通常， 在web开发中，有下面几种形式，会引起URL的变化：

1. <a href/></a>  (通常都是由用户来点击的）
2. <form action='/some_path'/> 也是由用户来点击提交的。
3. 由js引起的：
  3.1 location.href='www.baidu.com'
  3.2 js 触发 a的 click事件，间接实现点击
  3.3 js 触发 form的 submit 事件，间接实现点击。

### 处理路由


对于一个请求，  http://a.com/apples/all?name=red

好的路由，应该有一个分发机制：
把某个url 分配给某个特定的代码来处理。 这个特定的代码，就叫做 controller中的 action。

例如：  /apples/all    =>   apples_controller 的  all action
例如：  /apples/update    =>   apples_controller 的  update action


我们就给出一个配置文件：

   match '/apples/all', 'apples#all'
   match '/apples/update', 'apples#update'

然后，我们有个 controller:

class ApplesController

  def all
    render 'all.html'
  end

  def update
  end
end


all.html:

<html>
  这里显示所有的 apple.
</html>


- 必要的表单辅助方法

表单，是web开发的重中之重。 永远离不开的内容。 基本是日常工作必备。 所以： 框架对于表单的处理，就特别重要。

例如：  我们修改(注意不是新增） 用户信息的页面，  小王 是 男性。 那么， 页面打开后，应该默认把小王的性别，显示男。


<select name='sex' >
  <option value=''>请选择</option>
  <option value='男' selected>男</option>
  <option value='女'>女</option>
</select>

难点就来了。 如果只用PHP， JSP的话，页面是这样的：
需要两个点：
1. 列出所有的选项
2. 列出默认选中的选项。

# JSP代码： 列出默认选中的项。
<select name='sex' >
  <option value=''>请选择</option>
  <option value='男'
  <% if(user.sex == "男"){ %>
  selected
  <% } %>
  > 男</option>
  <option value='女'
  <% if(user.sex == "女"){ %>
  selected
  <% } %>
  >女</option>
</select>

# JSP 代码： 列出所有的选项：

<% String<String>[] sex_options = {"男", "女"} %>

<select name='sex' >
  <option value=''>请选择</option>
  <% for(int i=0; i< sex_options.length; i++ ){ %>
  <option value='<%= sex_options[i] %>' selected><%= sex_options[i] %></option>
  <% } %>
</select>

# 把上面两个代码结合到一起：(又显示所有选项，又要加上默认选中项）

<% String<String>[] sex_options = {"男", "女"} %>

<select name='sex' >
  <option value=''>请选择</option>
  <% for(int i=0; i< sex_options.length; i++ ){ %>
  <option value='<%= sex_options[i] %>'
    <% if(user.sex == "sex_options[i]"){ %>
    selected
    <% } %>
  ><%= sex_options[i] %></option>
  <% } %>
</select>


超级罗嗦。

所以，我们需要一个 表单辅助方法。 一两行代码解决。

<%= select_tag("sex" , options_for_select(["男", "女"], user.sex) )%>

select_tag 专门负责生成：  <select name=...>  </select>

options_for_select 专门负责生成：  

  <option value=''>请选择</option>
  <option value='男' selected>男</option>
  <option value='女'>女</option>

所以，Rails, 对于老鸟来说（一般都是从java转过来的），都觉得太简洁了！
对于很多新手来说，入了rails的门槛，他再去学习其他的语言，会觉得，其他语言好复杂！

## 表单对象。

太重要了。回头单独讲。

## 良好的页面渲染

### Layout

比如，某个项目中，每个页面，看起来都是这样的：
<html>
  <head>
    <style.../>
    <javascript .../>
    <title>..</title>
  </head>
  <body>
    <div clas='menu'>...</div>
    <!-- 只有这里的内容是不一样的-->
    <div clas='footer'>...</div>
  </body>
</html>

所以，好的框架，现在都支持一个概念： 布局。 (layout)

多个页面，可以共用一个布局。
例如， 对某个资源的 增删该查，列表页，我只需要 显示 相关的内容就可以了（ form, table ... )
其他的 <head> <footer/>等公共使用的内容，都不需要单独再写一次。

目前来看， Rails, angular, Vuejs 都实现了这个功能。

### 页面的片段（partial,  fragment 等等）

例如，好多页面，，都用到了：页面的footer.(显示版权信息）

1.html

<div> 这里是 1 的内容 </div>
<div class="footer">
  copyright @ 2016 xx co.ltd ...
</div>


2.html

<div> 这里是 2 的内容 </div>
<div class="footer">
  copyright @ 2016 xx co.ltd ...
</div>


在好的框架中， 就可以把 footer给提取出来。成为一个独立的文件
例如：

_footer.html.erb (例如规定，公共的视图片段，使用 _ 开头。)
<div class="footer">
  copyright @ 2016 xx co.ltd ...
</div>


1.html

<div> 这里是 1 的内容 </div>
<%= render '_footer' %>

2.html

<div> 这里是 2 的内容 </div>
<%= render '_footer' %>

非常简单，但是极其有效。能让你的代码量大幅减少。

我刚进优酷的时候， 项目是4万行代码。 (算的不精准, 所有回车都算了）
我没日没夜的 开发，维护， 不断的增加新功能， 功能数量翻了一倍多， 8个月以后，查看代码量， 2万行。
原来项目： 到处都是重复的代码。
所以我就一边重构（就像上面的方法，删掉重复代码，把公共的HTML抽取成片段，然后共用），一边加新功能。

## 数据库迁移

### 概念

对表结构的 修改的管理（可以按照顺序来演进，修改，也可以按照反向的顺序来撤销）
the management of incremental, reversible changes to relational database schemas

### 为什么要用它？

我们直到，管理代码，用SCM(git, svn)
管理数据库的表结构，用 database migration

git ： 可以取出任意时刻的代码
migration: 可以取出任意时刻的你的数据库的表结构。（通过 migrate, 或者  rollback 来实现的）

所以，它的出现，是为了解决问题：

小王和 小李， 一起在做一个项目。

小王，昨天，新增了两个表，改动一个表（删除了一个列，又增加了2个列）

小李，今天早上9点，更新代码，然后运行。崩溃了。

小李怎么办？

1. 问小王： 我崩了。 小王回答：笨蛋。我把我的数据库导出一份给你。
小李说，好的，你等着。

第二天，小王更新代码，崩溃了。 问小李.

所以，这个项目组，以后，谁改动数据库，都得吼一声。 (low)

好一点儿的项目组：  每次数据库结构有了更新， 都要导出一个 表的结构的文件, 放到SCM（GIT/SVN）中。其他人
更新代码后，都要把这个表结构文件导入到数据库。

于是。表的结构问题解决了。

但是，更大的问题来了： 原来的测试数据都没了。

小王 开发一个功能， 需要用到测试数据，辛辛苦苦录入了4个表，每个表10条数据。 更新完代码后，
他是不敢 导入新的数据库的表结构的。

小王还的把自己的数据库的文件导出出来，然后，跟远程的合并。

远程的代码下载完后，小李不乐意了。你干嘛把我的机器上的测试数据给覆盖了？

而且每次升级，都是个噩梦。
老板不乐意了： 你俩咋把测试数据，放到正式服务器的数据库上了？

所以，我们的database migration就是为了解决这个问题的。


方式：

把对数据库的操作，变成：
   1. 不是通过SQL语句来人肉修改。 而是：通过代码来修改。（把对数据库的操作，放到专门的代码文件中，例如  db/migration/*.rb, *.java  */
   2. 一个migration, 占用一个文件。

### 例子

我希望创建一个表：

1. 新建一个文件：    db/migration/001-create-apples.rb

class CreateApples
  def self.up
    create_table 'apples' do  |t|
      add_column 'name', String
      add_column 'color', String
    end
  end

  def self.down
    drop_table 'apples'
  end
end

    1. 任何一个migration, 都要有 up, down, 这样的话，它才能迁移。（前进的时候调用up, rollback 的时候，调用down)
    2. up 和 down, 永远是对立的操作。

2. 运行migration

$ rake db:migrate

这个migration 就会通过某种方式， 来生成SQL， 并且执行。

self.up : 就会生成： create table 'apples' .....
slef.down:           drop table 'apples' ..


有了这个形式， 小王和小李， 就happy了。

每次小王，都把对数据库的改动，写道migration中，小李更新完代码后，直接执行 rake db:migrate 就可以了。
原来的数据还能保留。

如果在部署的时候发现，新的数据库结构不合理， 还能rollback回去。

## 新手军规

不要用 rails g 这个命令。

除了migration, 无论是view, 还是controller,  还是什么，都不要用 rails generate  这个命令。

对于新手来说特别重要！

人肉的敲入命令，能加深大家的记忆和理解。
