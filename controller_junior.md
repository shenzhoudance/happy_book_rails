# 从controller中读取request的参数

## 军规

1. 7个默认的路由，不能被覆盖。 例如：

```
resouces :books do
  collection do
    get :new  # 这样不行，不要重复定义路由。
  end
end
```

2. redirect_to 或者 render 只能有一个。并且这两者都只能出现在 action的最后一行.
他们相当于方法的return， 执行完之后，不会往下面继续执行。

```
  def create
    Teacher.create :name => params[:the_name]
    render :text => 'ok'
    redirect_to books_path
  end
```

## 开始之前

记得修改  application_controller.rb , 把它的 把它的protect_from_forgery 注释掉。

```
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  #protect_from_forgery with: :exception
end
```

上面的代码，要求我们提交表单时，多提交几个参数。我们从学习的目的出发，暂时先把它注释掉。

## 参数的来源：  两种：

1.url .  例如:  `/books?name=三体`

2.form.

```
  <form action='/foo'  method='POST'>
    <input type=text name='keyword' />
    <input type=submit value='搜索' />
  </form>
```

## 使用params读取参数。

### 读取 url中的参数

对于请求: `GET /books?name=三`

在 controller中：

```
def index
  name = params[:name]
  @books = Books.where("name like '%#{name}%' ")
end
```

希望知道 params是什么，就给他打印出来：

```
# 任何对象在ruby中，都可以通过  inspect 来查看。 极其方便
puts params.inspect

{"name"=>"aaa", "controller"=>"books", "action"=>"index"}
```

### 在form 中，读取参数

1.假设页面有个搜索框：   请输入 书名

```
<form action='/books' method='GET' >
  <input type='text' name='query' />
  <input type='submit' value='查询！'/>
</form>
```

2.在controller中，读取参数。

```
params[:query]
```

无论是POST， 还是GET， 过来的参数，都是一个hash, 都用 params[:key] 来获取。

```
puts params.inspect

# => {"query"=>"c", "controller"=>"books", "action"=>"index"}
```

# 读取参数并操作数据库

## 新建

1.小王，打开 “新建页面“

```
 get  /books/new  # rails 的路由
```

2.在视图中，创建： `app/views/books/new.html.erb`

```
请输入：
<form action='/books' method='POST'>
  <input type='text' name='the_name'/>
  <input type='submit' value='确定'/>
</form>
```

3.在controller中：

```
  def create
    Teacher.create :name => params[:the_name]
    render :text => 'ok'
  end
```

4.继续完善controller:

创建好之后，自动跳转到： /books

```
  def create
    Teacher.create :name => params[:the_name]
    redirect_to books_path
  end
```

## 编辑

1.来到编辑页

```
GET /books/:id/edit   (假设 id = 2 )
```

2.在列表页中， 为每个记录，都增加一个链接：

```
<% @teachers.each do |teacher| %>
  <%= teacher.inspect%> ,
  <%= link_to '编辑', edit_book_path( :id => teacher.id )%>
  <%= link_to "删除", "/books/#{teacher.id}", :method => 'delete'%>
<br/>
<% end %>
```

3.增加：  `edit action`

```
  def edit
    @teacher = Teacher.where("id = #{params[:id]}").first
    @teacher = Teacher.where("id =  ? ", params[:id]).first
    @teacher = Teacher.find params[:id]
  end
```

  上面的三种写法， 第二种和第三种都可以。
  查询一个对象（记录）的话，用： find(id)
  查询：一堆的对象的华， 用where.

4.在view中， 增加表单的 默认值：
(以后我会教大家，使用表单对象 form_for 来简化 ）
```
  <input type='text' name='the_name' value='<%= @teacher.name %>'/>
```

5.继续修改view:

```
  <input type='hidden' name='_method' value='put' />
```
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  Rails才会把这个form 用 对应的 action来处理 （  update  ）

6.最后一次修改view:
修改 form的 action：
```
<form action="/books/<%= @teacher.id %>/edit" method='POST'>
```

完整的view

```
请输入：
<form action="/books/<%= @teacher.id %>" method='POST'>
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  <input type='hidden' name='_method' value='put' />
  <input type='text' name='the_name' value='<%= @teacher.name %>'/>
  <input type='submit' value='确定'/>
</form>
```

生成的 html例子：


```
请输入：
<form action="/books/14" method='POST'>
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  <input type='hidden' name='_method' value='put' />
  <input type='text' name='the_name' value='linux的安装调试'/>
  <input type='submit' value='确定'/>
</form>
```

输入值，点击 确认， 就会发起一个请求：

```
PUT /books/14    (  rails 路由：  put /books/:id )
```

7.增加 ： update action:
```
  def update
    @teacher = Teacher.find params[:id]
    @teacher.name = params[:the_name]
    @teacher.save
    redirect_to books_path
  end
```

## 作业

实现通过参数创建或者编辑一个东东.
