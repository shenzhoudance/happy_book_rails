在 controller中，  在 create/ update/ 方法的尾部， 我们需要跳转到其他URL。
会看到这样的代码：

```
def create
  book = Book.create params[:book]
  redirect_to book
end
```

上面的 redirect_to book, 等同于：

```
  redirect_to book
  redirect_to book_path(:id => book.id)
  redirect_to '/books/' + book.id
```

# 作业

1. 新建rails项目, market
2. 新建两个页面:

　　水果列表页：　　/fruits/list

    内容显示　三种水果列表就可以了．

    水果的新建页　    /fruits/new

    显示啥都行，
3. 列表页中，下方有个链接，　可以跳转到　新建页．

4. 新建页中，下方有个链接，　可以跳转到　列表页．

要使用　named_routes  (xx_path) 这样的形式．　

例如：　错误：　<a href='/fruits/list'>xx</a>
例如：　正确：　<a href='<%= list_fruits_path %>'>xx</a>
或者：　<%= link_to 'xx', list_fruits_path %>

5. 要有root_path (访问　 /  的时候，　要显示页面，"欢迎来到某某水果超市!" )

6. 有个接口:  访问 /interface/fruits/all 的时候, 要给到json结果:
(提示: 使用namespace路由)

```
{
  "result": ['香蕉', '苹果', '橘子']
}

```

(渲染json 的时候, 使用 render :json => ... )

7. 还可以访问这个url:  /taste_good ,  显示页面:  "好好吃啊!"
(提示,使用 match ... to 的写法)


