# 单元测试介绍。

历史： 最早的测试框架，是 Junit. 作者 Kent Beck.  是敏捷开发大师，他是那种，写了几十年代码的人。

最早的Junit帮了大家的大忙。 解救人于水火。导致了Junit 出现之后， Nunit( Net) , CUnit( C/c++) ， jsUnit,
以及现在的ruby的 test unit. 都纷纷出现。

概念： unit test.  测试最小的代码粒度。

100

10 x 1000 = 10000

## 单元测试产生的缘由

如何保证项目是正常的？

大家的很常见共识：  在钢丝绳上跳舞。

做了某个修改之后，如何保证你的修改，没有破坏其他功能正常的函数？

1. 人肉测试 —— 把所有的功能都测试一下吗 ?
   第一次，可以。 90%
   第二 。  开始烦了。  测试 60%
   第三 ？
   第四？

出现了一个问题，或者一个想法： 让之前的人肉测试过程，可以自动重播（re-play)

催生出了一个理论：

人肉测试的步骤，最好是可以被 代码记录下来的。这样才能方便重现。

例如： 一个方法：

```ruby
def sum a, b
  return a + b
end
```


如何测试这个方法呢？

```
irb,
> sum 1, 2
# => 3
```

上面是人肉测试过程。   这个步骤里面，我们可以提取出，用代码重现的步骤。

```
sum 1,2
（肉眼对比， sum 1,2 给出的结果， 是否是3 )
```

那么，我们就希望有一种  框架(framework) ,或者一种工具，能够帮我们把上述的过程给重新运行。

从上面的过程可以看出, 自动化的测试，需要两个重要过程：

1. 记录代码。      =>  我们人肉肯定是要做的。但是，只做一次。
2. 运行代码。      =>  依赖于一条命令。就可以。

所以，我们可以吧代码， 按照作用，分成两种：

1. 实现代码。 它是来实现需求的。  ( implementation code)
2. 测试代码。  测试 实现代码的。  ( test code)

所以，上面的人肉对比过程，就可以：

```
# 这个是  实现代码
def sum a ,b
  a + b
end


# 下面是测试代码。
def test_sum
  temp = sum(1,2)
  assert temp == 3
end
```

因为 已经有这样的单元测试 框架了（工具）， test_unit. 所以，我们就这样用：

经典命名方式：

 任何一个class, 都要 命名成：  AbcTest
 该test Case中的测试方法，包含两种：
   before/after   在某个测试执行之前、之后的方法
   test_xyz       测试方法

```
#  sum_test.rb

def sum a,b
  a + b
end


require 'test/unit'

class MyTest < Test::Unit::TestCase
  def test_sum
    assert sum(1,2) == 3
  end
end

```

运行：


```
Run options:

# Running tests:

hihihi
.

Finished tests in 0.000769s, 1300.3901 tests/s, 0.0000 assertions/s.

1 tests, 0 assertions, 0 failures, 0 errors, 0 skips
ruby hi_test.rb
Run options:

# Running tests:

.

Finished tests in 0.000432s, 2314.8148 tests/s, 2314.8148 assertions/s.

1 tests, 1 assertions, 0 failures, 0 errors, 0 skips
```

这样就测试了 `sum` 这个方法。

但是这个例子太简单了。

我们现实当中，需要测试：

1. 对于数据库的操作
2. 对于HTTP的请求。 （鼠标点连接， 提交表单等等）

所以，我们需要在Rails中，使用单元测试。

默认情况下，rails是使用 test-unit 这个框架作为单元测试的。

```
▾ test/
  ▸ controllers/
  ▸ fixtures/
  ▸ helpers/
  ▸ integration/
  ▸ mailers/
  ▸ models/
  test_helper.rb
```

其中的 test 目录，是默认的放置测试文件的目录。 这里，
test_helper.rb文件就是测试的配置文件。

test_helper中，定义了在Rails环境下的TestCase.

```ruby
require 'active_support/testing/autorun'
require 'active_support/test_case'
require 'action_controller'
require 'action_controller/test_case'
require 'action_dispatch/testing/integration'
require 'rails/generators/test_case'
```

所以，有了 test目录下的 test_helper.rb, 我们才能在Rails中，
运行单元测试（操作数据库，模拟发送HTTP  GET/POST/DELETE请求)

# 该如何做？

假设：
1. 我们有个Rails项目。
2. 该项目有个功能点： /books.  每个book, 有个属性：title:string
3. 当前所有的功能都正常。

## 先写一个针对 books controller的测试。

```ruby
# controller:
class BooksController < ApplicationController
  def index
    @books = Book.all
  end
  # ...
end
```
那么，测试文件就是：

```ruby
# test/controllers/books_controller_test.rb
require 'test_helper'

class BooksControllerTest < ActionController::TestCase
  setup do
    @book = Book.create :title => 'book 1 name'
  end

  def test_should_get_index

    # 我要访问：  /books
    get :index

    # 下面这句，表示， 页面正常打开， 返回http 200
    assert_response :success
  end
end

```

运行：
```bash
 $ bundle exec rake test:functionals
Run options: --seed 10284

# Running:

.

Finished in 0.628374s, 1.5914 runs/s, 1.5914 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

说明刚才的单元测试是没错的。

但是可能大家没有啥感觉。

所以，我就把代码做点儿更改：

```ruby
# test/controllers/books_controller_test.rb
require 'test_helper'

class BooksControllerTest < ActionController::TestCase
  setup do
    @book = Book.create :title => 'book 1 name'
  end

  def test_should_get_index
    get :index
    puts "lalalalalalalala, failed"
    raise "failed"
    assert_response :success
  end
end

```

运行它，可以看到，报错了：

```bash
$ bundle exec rake test:functionals
Run options: --seed 24385

# Running:

lalalalalalalala, failed
E

Finished in 0.149283s, 6.6987 runs/s, 0.0000 assertions/s.

  1) Error:
BooksControllerTest#test_should_get_index:
RuntimeError: failed
    test/controllers/books_controller_test.rb:63:in `test_should_get_index'

1 runs, 0 assertions, 0 failures, 1 errors, 0 skips
```

可能大家还是没啥感觉。 所以，我们按照真实的例子来：

正常的  /books 页面看起来是：
TODO 补充好图片。 保存在了桌面上。

那么，假设我们在修改代码的时候，它出错了。

```ruby
class BooksController < ApplicationController
  before_action :set_book, only: [:show, :edit, :update, :destroy]

  def index
    raise "I got error!"  # => 这里故意让controller出错！

    @books = Book.all
  end
```

重新打开  '/books':

TODO 出错的图片。

这个时候，我们运行单元测试：

```bash
$ bundle exec rake test:functionals
Run options: --seed 36707

# Running:

E

Finished in 0.133835s, 7.4719 runs/s, 0.0000 assertions/s.

  1) Error:
BooksControllerTest#test_should_get_index:
RuntimeError: I got error!
    app/controllers/books_controller.rb:7:in `index'
    test/controllers/books_controller_test.rb:61:in `test_should_get_index'

1 runs, 0 assertions, 0 failures, 1 errors, 0 skips

```

不要小看这个小的case, 一个真实的项目中，有几百个需要测试的功能点是
常事儿。  那么，我们在每次项目部署的时候，都需要保证所有的
功能是正常运行的。 我们不应该每次都人肉做这个事情。

所以，一定要用单元测试（来实现测试的自动化）.

## 再来个对 Model的测试

model的代码：

```ruby
#app/models/book.rb
class Book < ActiveRecord::Base
end
```
对应的单元测试代码就是:

```ruby
# -*- encoding : utf-8 -*-
require 'test_helper'

class BookTest < ActiveSupport::TestCase
  def test_save
    Book.create :title => "三体"
    Book.create :title => "球状闪电"
    Book.create :title => "海伯利安"

    assert Book.count == 3
  end
end
```

如果没有其他测试数据的话， 上面的代码就会正常运行：

``j
$ bundle exec rake test:units
Run options: --seed 60624

# Running:

=====
#<ActiveRecord::Relation [
 #<Book id: 980190963, title: "三体", created_at: "2015-10-23 07:33:10", updated_at: "2015-10-23 07:33:10">,
 #<Book id: 980190964, title: "球状闪电", created_at: "2015-10-23 07:33:10", updated_at: "2015-10-23 07:33:10">,
 #<Book id: 980190965, title: "海伯利安", created_at: "2015-10-23 07:33:10", updated_at: "2015-10-23 07:33:10">]
>
.

Finished in 0.061792s, 16.1833 runs/s, 16.1833 assertions/s.

1 runs, 1 assertions, 0 failures, 0 errors, 0 skips
```

但是，我们实际当中，发现报错，于是我们增加调试信息：

```ruby

  def test_save
    # 往数据库中，新增3个book
    Book.create :title => "三体"
    Book.create :title => "球状闪电"
    Book.create :title => "海伯利安"

    # 打印数据库中的数据
    puts "===== "
    puts Book.all.inspect

    assert Book.count == 3
  end
```

于是发现，最开始，由于使用scaffold, 生成了测试数据(test/fixtures/books.yml)
导致了，数据库中，不但存在了上面的三个Book, 还有另外两个：title 等于 "MyString" 的Book.

```bash
$ bundle exec rake test:units
Run options: --seed 55101

# Running:

=====
#<ActiveRecord::Relation [
 #<Book id: 298486374, title: "MyString", created_at: "2015-10-23 07:31:05", updated_at: "2015-10-23 07:31:05">,
 #<Book id: 980190962, title: "MyString", created_at: "2015-10-23 07:31:05", updated_at: "2015-10-23 07:31:05">,
 #<Book id: 980190963, title: "三体", created_at: "2015-10-23 07:31:05", updated_at: "2015-10-23 07:31:05">,
 #<Book id: 980190964, title: "球状闪电", created_at: "2015-10-23 07:31:05", updated_at: "2015-10-23 07:31:05">,
 #<Book id: 980190965, title: "海伯利安", created_at: "2015-10-23 07:31:05", updated_at: "2015-10-23 07:31:05">]>
F

Finished in 0.052311s, 19.1164 runs/s, 19.1164 assertions/s.

  1) Failure:
BookTest#test_save [/workspace/test_rails/test/models/book_test.rb:12]:
Failed assertion, no message given.

1 runs, 1 assertions, 1 failures, 0 errors, 0 skips

```
这是为啥呢？ 就是因为， Rails中的单元测试，在每次运行前，都会
自动加载 fixtures.

```yaml
# test/fixtures/books.yml

book_one:
  title: MyString

two:
  title: MyString
```

## setup 与 teardown

setup: 在每个 testcase 运行之前， 运行。

teardown:  在每个testcase 运行完之后，运行。

例子：

```ruby

require 'test_helper'

class BooksControllerTest < ActionController::TestCase
  def setup
    puts "== before run test case"
  end

  def test_should_get_index
    get :index
    assert_response :success
  end

  def test_should_get_show
    @book = Book.create :title => 'book 1 name'
    get :show,  :id => @book.id
    assert_response :success
  end

  def teardown
    puts "== after run test case"
  end
end
```

结果：可以看到， setup, teardown 分别执行了两次。

```bash
$ bundle exec rake test:functionals
Run options: --seed 13734

# Running:

== before run test case
== after run test case
.== before run test case
== after run test case
.

Finished in 0.162701s, 12.2925 runs/s, 12.2925 assertions/s.

2 runs, 2 assertions, 0 failures, 0 errors, 0 skips
```

## 可读性更好的一种变形：

下面两种对于test case 的写法，是相同的
```ruby

def test_sum
  assert 1+2 ==  3
end

# 这种写法就是受到了 rspec的巨大影响。
test "1 + 2 应该等于3" do
  assett 1+2 == 3
end
```

```ruby
# 下面两种写法，相同
def setup
  Book.create :title => 'the name'
end

setup do
  Book.create :title => 'the name'
end
```

同理， teardown 也是。

下面是个完整的例子，大家可以参考来写。

原则：

1. 基本的增删改查，必须要写单元测试。
2. 单元测试中，必须要有用户的登录。
3. 上传文件，或者发送邮件，或者其他比较麻烦的，可以不写。
4. 针对controller的测试，必须写。model的测试，可以有选择的写（比如说，特别复杂的关联关系，或者某个方法），
5. 其他的，request, view, routes 不需要写单元测试。


```ruby
# test/controllers/books_controller_test.rb
# -*- encoding : utf-8 -*-
require 'test_helper'

class BooksControllerTest < ActionController::TestCase
  setup do
    @book = Book.create :title => 'book 1 name'
  end

  test 'should get index' do
    get :index
    assert_response :success
  end

  test 'get show ' do
    get :show,  :id => @book.id
    assert_response :success
  end

  test 'get new' do
    get :new
    assert_response :success
  end

  test 'post create' do
    post :create, :book => {
      :title => '三体'
    }
    assert Book.last.title  == '三体'
  end

  test 'delete destroy' do
    id_to_delete = @book.id
    delete :destroy , :id => id_to_delete
    assert Book.find_by_id(id_to_delete).blank?
  end

  test 'should get edit' do
    get :edit, :id => @book.id
    assert_response :success
  end

  test 'should put update' do
    put :update, :id => @book.id , :book => {
      :title => '球状闪电'
    }

    assert Book.find(@book.id).title == '球状闪电'
  end

end
```

注意：

单元测试所用的数据库，必须跟其他的数据库（development, production)
分开，因为每次运行单元测试的时候，都会把测试数据库中的数据，
删掉，重写。 所以，config/database.yml中，你总会看到这句话：

```
 16 # Warning: The database defined as "test" will be erased and
 17 # re-generated from your development database when you run "rake".
 18 # Do not set this db to the same as development or production.
```

但是，实践中，一个常见的手法是，在部署时，development, production 可以
弄成一个。
