# RESTful 路由

## 官方文档中不用看的内容。

只学： resources :xx 就足够了。

了解一些： namespace.
了解一些： match
了解一些:  get ... to
就够了。

其他的都不用看。
非资源式路由。
定制资源式路由，不用看。

我们在项目中， 不用那些稀奇古怪的知识。

## 学习的目标

看到 resources, 能瞬间人肉算出它生成的7中路由， 能瞬间
说出某个路由对应的action。


## 下面是大师的讲解。

就是使用不同的请求类型， 来获取各种资源。

```
POST: create,  (创建资源）
PUT/PATCH： update, (更新）
GET:  index, show, new, edit, (只读取数据，不修改数据)
DELETE: destroy (删除)
```

所以， 在RESTful中，不同的http request类型，就决定了你是要对数据库进行
什么操作（增，删，改，还是只读？）

至于rails, 是如何实现对RESTful进行解析的呢？

极其简单：

config/routes.rb中：
```
resources :users
```
(注意： 不是 resource )
上面一句，就直接定义了7种路由：

```
GET  /users         index     显示 user的列表页
GET  /users/new     new       显示 user的新建页面。
GET  /users/3       show      显示id是3的user
GET  /users/3/edit  edit      显示 user(id =3)的编辑页面。
PUT  /users/3       update    对id = 3的user进行修改 （后面还会紧跟一大串的参数)
POST /users         create    对users进行创建（后面也有一大堆参数)
DELETE  /users/3    destroy   对 id=3的 user 进行删除操作。
```
(可以使用 $ rake routes 就可以查看当前项目中所有的路由）

这就是一种： convention(约定） over configuration (配置)

## Rails中的各种 _path, _url 的来历

下面的代码, 来自某个项目中的命令:  `$ rake routes`:

```
             POST     /travels(.:format)             travels#create
  new_travel GET      /travels/new(.:format)         travels#new
 edit_travel GET      /travels/:id/edit(.:format)    travels#edit
      travel GET      /travels/:id(.:format)         travels#show
             PATCH    /travels/:id(.:format)         travels#update
             PUT      /travels/:id(.:format)         travels#update
             DELETE   /travels/:id(.:format)         travels#destroy
```

我把它整理一下,大家就知道内容是什么了:

|url 的名字|REST方法 | 对应的url 的表达式  |  对应的 controller#action|
|--:|---|---|---|
|travels_path       | POST    |  /travels(.:format)  |           travels#create|
|new_travel_path | GET     |  /travels/new(.:format) |        travels#new|
|edit_travel_path | GET     | /travels/:id/edit(.:format)   | travels#edit|
|travel_path | GET     | /travels/:id(.:format)        | travels#show|
|travel_path | PATCH   | /travels/:id(.:format)        | travels#update|
|travel_path | PUT     | /travels/:id(.:format)        | travels#update|
|travel_path | DELETE  | /travels/:id(.:format)        | travels#destroy|

所以, 看到第一列了吗? 大家见到的 `xx_path`, `xx_url` 就是从这里来的. 另外, `travel_path`等同于`travel_url`.


## edit_user_path @user 是什么东东?

如何编辑某个user?

"/users/1/edit"

写成ruby代码:
1. "/users/" + user.id + "/edit"
2. "/users/#{user.id}/edit"  # string interpolation 插入插值

实际上上面这两种形式，都是外行的风格（比如，之前做java 的同学，
来写ruby , 就是这个风格）

下面是 Rails风格(把可读性发挥到极致）的写法：

最初的形式：
```
<%= edit_user_url({:id => User.first.id}) %>
```

然后，ruby的最外层的方法调用的 ()可以省略：
```
<%= edit_user_url {:id => User.first.id} %> <br/>
```

ruby的最外层的 hash的 {} 可以省略：

```
<%= edit_user_url :id => User.first.id %> <br/>
```

对于rails来说，每个数据库的对象，转换成 string interpolation 的时候，都是默认调用
id方法。

所以,一个默认的model  当它 to_string的时候, 是要返回 id 的.

所以,最终,也就有了这样的写法:

```
<%= edit_user_url User.first %>
```


==== 有空使劲看:  http://guides.rubyonrails.org/routing.html
有 各种  _url,  _path 的来历和用法.

resources :countries do
  resources :provinces do
    resources :cities do
      resources :districts do
         ...
      end
    end
  end
end

