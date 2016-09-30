# 读取html form， 操作数据库

## 新建

1. 小王，打开 “新建页面“

 get  /books/new  # rails 的路由

2. 在视图中，创建：
(app/views/books/new.html.erb)

请输入：
<form action='/books' method='POST'>
  <input type='text' name='the_name'/>
  <input type='submit' value='确定'/>
</form>

3. 在controller中：

  def create
    Teacher.create :name => params[:the_name]
    render :text => 'ok'
  end

4. 继续完善controller:

创建好之后，自动跳转到： /books

  def create
    Teacher.create :name => params[:the_name]
    redirect_to books_path
  end

## 编辑

1. 来到编辑页

GET /books/:id/edit   (假设 id = 2 )

2. 在列表页中， 为每个记录，都增加一个链接：

id: 1,    name: 小王。  操作：  <a href='/books/1/edit'>编辑</a>


<% @teachers.each do |teacher| %>
  <%= teacher.inspect%> ,
  <%= link_to '编辑', edit_book_path( :id => teacher.id )%>
  <%= link_to "删除", "/books/#{teacher.id}", :method => 'delete'%>
<br/>
<% end %>

3. 增加：  edit action

  def edit
    @teacher = Teacher.where("id = #{params[:id]}").first
    @teacher = Teacher.where("id =  ? ", params[:id]).first
    @teacher = Teacher.find params[:id]
  end

  上面的三种写法， 第二种和第三种都可以。
  查询一个对象（记录）的话，用： find(id)
  查询：一堆的对象的华， 用where.

4. 在view中， 增加表单的 默认值：
(以后我会教大家，使用表单对象 form_for 来简化 ）
  <input type='text' name='the_name' value='<%= @teacher.name %>'/>

5. 继续修改view:
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  <input type='hidden' name='_method' value='put' />
  Rails才会把这个form 用 对应的 action来处理 （  update  ）

6. 最后一次修改view:
修改 form的 action：
<form action="/books/<%= @teacher.id %>/edit" method='POST'>

完整的view


请输入：
<form action="/books/<%= @teacher.id %>" method='POST'>
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  <input type='hidden' name='_method' value='put' />
  <input type='text' name='the_name' value='<%= @teacher.name %>'/>
  <input type='submit' value='确定'/>
</form>

生成的 html例子：


请输入：
<form action="/books/14" method='POST'>
  <!-- 提交一个值， _method=put, 告诉rails 这个form 是以 put 形式发起的 请求 -->
  <input type='hidden' name='_method' value='put' />
  <input type='text' name='the_name' value='linux的安装调试'/>
  <input type='submit' value='确定'/>
</form>

输入值，点击 确认， 就会发起一个请求：

PUT /books/14    (  rails 路由：  put /books/:id )

7. 增加 ： update action:

  def update
    @teacher = Teacher.find params[:id]
    @teacher.name = params[:the_name]
    @teacher.save
    redirect_to books_path
  end
