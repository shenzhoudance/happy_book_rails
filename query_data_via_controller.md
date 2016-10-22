# Model , View, Controller小结合

例子：

(前提：  数据库中，有个lessons表， 表中，有若干数据）

小王， 打开浏览器， 希望看到所有的lessons.

步骤：
1. 小王发起：  get ：    /lessons    OK.   (服务器没启动）
2. 启动服务器。  $ rails server   OK .   (少了action)
3. 实现index action

在服务器端, 我们要;

1.修改路由 `config/routes.rb`:

```
  resources :lessons

```

2.实现controller:


```
# app/controllers/lessons_controller.rb

class LessonsController < ApplicationControler
  def index
    # 用 @变量。 希望把 数据，从 controller， 传到 view,
    @lessons = Lesson.all
  end
end
```

3.实现Model: Lesson

增加文件: `app/models/lesson.rb`

```
class Lesson < ActiveRecord::Base
end
```

4.实现view视图： `app/views/lessons/index.html.erb`

```
<% @lessons.each do |lesson| %>
  <%= lesson.inspect %><br/>
<% end %>
```

5.于是运行 `$ rails server`, 在浏览器中, "localhost:3000/lessons", 就可以看到:
```
课程有：
#<Lesson id: 1, name: "课程一", student_id: "1", teacher_id: "1">
#<Lesson id: 2, name: "课程二", student_id: "1", teacher_id: "2">
#<Lesson id: 3, name: "课程三", student_id: "1", teacher_id: "3">
#<Lesson id: 4, name: "课程一", student_id: "2", teacher_id: "1">
```


6.细化它, 把它做成一个表格:

```
<table>
  <tr>
    <th>id</th>
    <th>Name</th>
    <th>student</th>
    <th>teacher</th>
  </tr>
<% @lessons.each do |lesson| %>
  <tr>
    <th><%= lesson.id %></th>
    <th><%= lesson.name %></th>
    <th><%= lesson.student_id %></th>
    <th><%= lesson.teacher_id %></th>
  </tr>
<% end %>
</table>
```

至此：  用户发起请求， 到controller处理， 到 使用model读取数据库， 并且显示。 就结束了。

# 引申：可以在任意controller中， 对任意表进行操作。

在books_controller中， 是不是只能显示books 的数据？

当然不是了。 上面的例子， books_controller,中， 显示lessons .

我们还可以在这一个controller中，显示3个表的数据

students, teachers, lessons 这三个表。都可以显示。

# 我们也可以在model中定义任意方法

例如: 我可以在Lesson 中定义方法 foo

```
class Lesson < ActiveRecord::Base
  def self.foo
    "I am the foo method"
  end
end
```

然后在 任意的 view中调用:

```
<%= Lesson.foo %>
```

# 引申： 在view中，显示级联的内容

例如：  在 lessons表中，显示： teacher,student的名字（不仅仅是id)

在view中：

原来：

```
    <th><%= lesson.student_id %></th>
    <th><%= lesson.teacher_id %></th>
```

改成：

```
    <th><%= lesson.student.inspect %></th>
    <th><%= lesson.teacher.name %></th>
```


(得益于 ActiveRecord 的 关联。 直接就可以显示了。)

如果用十年前的传统技术的话， 你需要写的这么傻：

```
      <% temp = Student.where("id = ?", lesson.student_id).first %>
      <%= temp.inspect %>
```

所以，这就是ORM的威力。

# 排序

@books = Book.all   # 根据id 排序   ,  Book.order('id asc')
@books = Book.order('id desc')
@books = Book.order('id desc').limit(2)

# 有.all 与 没有 .all 的区别

Book.all  # 立马查数据库。

@books = Book.order('id')  # 在controller中， 定义了查询，但是不会马上执行。（只在需要执行的时候执行）
....  我让程序休息60秒。
<%= @books %>   # 这个时刻，是不得不执行的时候了。才会执行

这个概念比较学究。不到优化的时候，用不上。 真要优化的话，这个概念也没太大用。（帮不上太大忙）

但是大家要了解。你才能看得懂别人的代码。

# 如果丧心病狂的话，希望回到15年前的编程方式。

所有代码都写到 erb中：
<%
   sons = Son.where('name like "%小%"')
%>
<% sons.each do |son| %>
  <%= son.name %>,
<% end %>

MVC: 最大的优点： 逻辑与 视图 是分离的。
几行代码无所谓。 几百行代码 混一起， 你就傻眼了。

为什么 PHP是被所有人鄙视的语言：

# 查询。

小王， 希望查看某些 名字中包含 xx 的 数据。

那么， 他访问的URL：  /books?name=一

就应该出现：  符合规则的数据。

## 获得 请求中的参数。

params[:name]  # 推荐这个。
params['name']


发起一个 delete 请求：

```
<% @teachers.each do |teacher| %>
  <%= teacher.inspect%> ,
  <%= link_to "删除", "/books/#{teacher.id}", :method => 'delete'%>
<br/>
<% end %>
```

