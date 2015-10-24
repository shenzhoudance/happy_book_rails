#Rails接口入门
使用Rails开发后台的时候，很多时候都需要给前端或者什么别的地方提供一些数据接口，这里我们就来讲讲如何入门Rails接口。
## 目录结构
一般之下，我们习惯把我们的controllers目录之下，如下：

+ app

  + controllers

    + interface

      + your_interface_controllers.rb

其实.rb文件的命名也是有规则的,以book为例。我们要写一个book的接口文件来暴露我们准备好的数据，所以按照rails命名规则，文件取名叫做books_controller.rb。
我们可以看看这个接口文件的结构内容：
```ruby
#books_controller.rb
class Interface::CasesController < ActionController::Base
#声明一个继承自ActionController::Base的类
  def all_books
  # 定义一个action来返回所有的book数据中的id,name字段
    all_books = Book.all.map do |book|
      {
        :id => book.id ,
        :name => book.name ,
      }
    end
    render :json =>{:result => all_books}
  end
end
```
以上就完成一个最简单的接口文件，其中定义一个接口叫all_books，然后我们要在routes.rb中配置好路由。打开routes.rb，插入如下代码：
```ruby
#在routes.rb中插入如下代码
namespace :interface do
  resource :book ,:only => [] do
    collection do
      get :all_books
    end
  end
end
```
这样就在路由文件中配置好了all_books的路由。

然后我们在浏览器中输入如下url就可以访问接口了，假设你用的是本地localhost:3000,则如下
http//:localhost:3000/interface/books/all_books
