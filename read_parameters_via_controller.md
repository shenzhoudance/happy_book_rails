# 从controller中读取request的参数

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

