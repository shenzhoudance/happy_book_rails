# 从controller中读取request的参数

参数的来源：  两种：

1. url .    /books?name=三体
2. form.
  <form action=''  method='POST'>
    <input type=text name='keyword' />
    <input type=submit value='搜索' />
  </form>

## 在url中读取参数。

1. GET /books?name=三

2. 在 controller中：

def index
  name = params[:name]
  @books = Books.where("name like '%#{name}%' ")
end

3. 希望知道 params是什么，就给他打印出来：

puts params.inspect  # 任何对象在ruby中，都可以通过  inspect 来查看。 #极其方便

{"name"=>"aaa", "controller"=>"books", "action"=>"index"}

## 在form 中，读取参数

1. 页面有个搜索框：   请输入 书名


<form action='/books' method='GET' >
  <input type='text' name='query' />
  <input type='submit' value='查询！'/>
</form>


2. 在controller中，读取参数。

```

  params[:query]
```

3. 无论是POST， 还是GET， 过来的参数，都是一个hash, 都用 params[:key] 来获取。
=== c
{"query"=>"c", "controller"=>"books", "action"=>"index"}
=======

(无论是在 java spring, 还是python 框架， get/post 获取参数的形式都不一样）

python:

para.get('key')
para.post('key')

产品经理 小李 ： 就问了：  我就是想获取个参数，干吗要知道这个参数是post/get?
就好像： 我想吃个苹果，我不在乎这个苹果，是辽宁的，还是陕西的。只要是好吃的就可以了。

4. 因为 form 是get形式，所以：

<form> 中， <input type=text name=query /> ，点击提交后， 页面的URL就会显示：

http://localhost:3000/books?query=a



