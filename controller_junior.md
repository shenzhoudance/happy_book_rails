# 从controller中读取request的参数

## 军规

1.7个默认的路由(`index`, `show`, `new`, `edit`, `update`, `destroy`, `create`)，不能被覆盖。 例如：

```
resouces :books do
  collection do
    get :new  # 这样不行，不要重复定义路由。
  end
end
```

2.redirect_to 或者 render 只能有一个。并且这两者都只能出现在 action的最后一行.
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

# Rails中，几个特殊的action

七个：

- index : 列表页
- new: 新建页。用户在该页面上， 输入一些数据，点击确定按钮后，会触发create action。
- create： 创建数据的action. 处理 new 中的form传递过来的参数，保存到数据库。
- edit: 显示编辑的页面。 用户在这个页面上，输入编辑的数据。 点击确定按钮后，会触发update action
- update ： 保存数据的action. 处理 edit 中的form传递过来的参数，保存到数据库。
- show : 详情页 (往往，用户是在列表页中，点击某一行的记录，就会跳转到该记录的详情页了）
- destroy : 删除action. (往往，用户也是在列表页中，点击某一行中的“删除”按钮，就会触发该action）

所以，上面7个action, 4个有页面（index, show, edit, new) , 3个无页面，直接操作数据库，操作完
之后，显示 某个页面（例如，显示列表页）



# 读取参数并操作数据库

我们先创建一个空的rails项目：　

```
$ rails new library
```

假设，我们在路由(`config/routes.rb`中，定义了：　

```
Rails.application.routes.draw do
  resources :books
end
```

并且，　我们定好对应的　controller:
编辑：`app/controllers/books_controller.rb

```
class BooksController < ApplicationController
end
```

## 列表  index

1.我们在controller中，创建action:

修改：　`app/controllers/books_controller.rb`
```
def index
  @books = Book.all
end
```

2.在view( `app/views/books/index.html.erb`) 中显示就可以了：

```
<% @books.each do |book| %>
   <%= book.inspect %>
<% end %>
```

3.在浏览器中，输入`/books` 就可以看到页面了。

## new 与 create

1.回顾下路由：　
```
GET /books/new
POST /books
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
  def new
  end

  def create
    Book.create :name => params[:the_name]
    redirect_to books_path
  end
```

4.这时候，用户直接打开　`/books/new`，　就可以看到　'new'这个页面。

5.输入数据之后，点击“确定”，　就会触发 `create` action
,就可以看到数据库中多了一条这个记录。 并且，会跳转到　`/books`这个页面。

## edit 与 update

1.回顾一下，我们的路由，`resources :books`会提供7种路由，跟edit相关的路由是：

```
GET /books/:id/edit
PUT /books/:id
PATCH /books/:id
```

PUT 与　PATCH 都对应着 `update` action. 所以，我们忽略PATCH这个路由。

2.在列表页中， 为每个记录，都增加一个链接：
修改 `app/views/books/index.html.erb`
```
<% @books.each do |book| %>
  <%= book.inspect%> ,
  <%= link_to '编辑', edit_book_path( :id => book.id )%>
  <br/>
<% end %>
```

3.在“编辑页”中，增加一个表单。
修改 `app/views/books/edit.html.erb`

```
请输入：
<form action='/books' method='POST'>
  <input type='text' name='the_name'/>
  <input type='submit' value='确定'/>
</form>
```

3.增加：  `edit action`

```
  def edit
    # 以下三种写法都可以。 一般用find
    @book = Book.where("id = #{params[:id]}").first
    @book = Book.where("id =  ? ", params[:id]).first
    @book = Book.find params[:id]
  end

```

  上面的三种写法， 第二种和第三种都可以。
  查询一个对象（记录）的话，用： find(id)
  查询：一堆的对象的华， 用where.

4.在view中， 增加表单的 默认值：
(以后我会教大家，使用表单对象 form_for 来简化 ）
把　原来的`input name='the_name'`,修改成：　
```
  <input type='text' name='the_name' value=<%= @book.name  %>/>
```

5.继续修改view, 增加下面的　ｈｉｄｄｅｎ标签:

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
    book = Book.find params[:id]
    book.name = params[:the_name]
    book.save
    redirect_to books_path
  end
```

## 查看详情

1.根据路由：

```
GET    /books/:id
```

2.在controller中，增加　

```
def show
  @book = Book.find [:id]
end
```

3.在view中，增加：
`app/views/books/show.html.erb`:
```
<h3>详情页</h3>
<%= @book.name %>
```

## 删除


1.根据路由：

```
DELETE /books/:id
```
2.在列表页中， 为每个记录，都增加一个链接：
修改 `app/views/books/index.html.erb`
```
<% @books.each do |book| %>
  <%= book.inspect%> ,
  <%= link_to "删除", "/books/#{book.id}", :method => 'delete'%>
  <br/>
<% end %>
```

3.在controller中，增加　

```
def destroy
  @book = Book.find [:id]
  @book.delete
  redirect_to books_path
end
```

4.在列表页中，点击任意一行记录对应的 删除，就可以触发这个destroy action了。
数据库就会删除对应的记录，然后页面会显示 “列表页”


## redirect_to 的用法

### 1.可以跳转到有效的URL

```
redirect_to books_path
redirect_to '/books'
```
### 2.也可以使用 back

```
redirect_to :back
```

## 作业

实现通过参数创建或者编辑一个东东.
