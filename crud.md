# Model 入门

## 学习收获

1. 知道如何进入Rails Console.
2. 知道持久层的概念
3. 知道如何在Rails中使用model来操作数据库.

Model是 MVC中的M, 作用是操作数据库。

## 数据持久层

数据持久层的缩写: persistant layer.  大家往往把它简称为: 持久层.

我们获取到一些数据的时候， 往往是这样：

```
  # 假设 这个变量，来自于 网络的其他服务器
  String content = SomeTool.query_from_remote ....

  # 如何把它保存到本地？
  经典方法： 保存到数据库

  # 不怕断电了。 数据就持久了。
  （明明很简单的概念，搞得很复杂, 持久层就是这么回事儿...）
```

所以，在实际项目中, 持久层有两个实现方式：

1. 保存到数据库. (这个是99%的选择)
2. 保存到文件.

所以，持久层的架构是：

```
本地代码  < === >  持久层  < === > 数据库/文件
```

## 从例子来看持久层

例如： 希望读取所有的book记录。

1. 浏览器发起一个请求给Rails. 例如`http://localhost:3000/books`
2. Rails调起持久层。 在 `books_controller#index_action` 中处理这个 `/books`请求。

```
class BooksController < ApplicationController
  def index
    @books = Book.all
  end
end
```

3. 持久层查询数据库。并且把数据加载过来。

```
Book.all
```

- `Book` 它是个model.  它映射到了 books 表。
- `Book.all`,  就会被Rails的持久层的机制，转换成一段SQL语句：

```
select * from books;
```

然后， Rails的持久层，再把上面的结果，从"数据库结果集"，转换成："ruby 代码" 的格式。

## 数据库结果集?

数据库结果集也就是 "ResultSet" (它很有名。几乎在所有语言连接数据库的时候，做查询的时候, 都要用到它)

下面是java代码中，访问mysql (这个就是所有语言访问mysql的过程)

```

// 第一步： 先建立一个连接。我也不知道干吗要搞个连接。 但是mysql就是这么规定的。
// 所以这个概念，对于程序员来说， 无意义。只是对mysql底层，才会有意义。
Connection con = DriverManager.getConnection(
    "jdbc:mysql://instance23389.db.xeround.com:15296/Inventory","user","password");

// 第二步： 再建立一个 prepared statement:
PreparedStatement Statement = con.prepareStatement("Select * from inventory");

// 第三步： 执行这个 prepareStatement 得到了 ResultSet
ResultSet result = Statement.executeQuery();


// 来一个一个的遍历这个结果
while (result.next()) {

		// 这一行， new Product(), 就是创建了一个java的对象。（在内存中）
    Product product = new Product();

		// result.getDouble : 获得某个列的值(还的预先知道这个列的类型）
    product.setTotal(result.getDouble("Total")); (result.getDouble. .. 是在硬盘中 -- mysql文件）
    product.setName(result.getString("Name"));
    // etc.

    productList.add(product);
}
```

上面的概念(ResultSet, Connection, PreparedConnection)虽然很学究,但是要读取数据库就是要这样的, 但是没它们不行,

## 数据库与持久层

数据库：
- sqlite:  是一个 .sqlite3 文件（例如 Rails中，默认就会创建：db/development.sqlite3)
- mysql:  也是一个文件。 对于ubuntu, 默认放在： /var/lib/mysql/数据库名下面，例如：
/var/lib/mysql/me/students.frm,  .ibd

文件放在硬盘上。

持久层： 以代码的形式来存在的。

可以认为 ， 持久层， 是对数据库的一种封装。

把 原始的数据库操作， 封装成 java/ruby/c 代码。

例如,
上面的， 10多行java代码， 转换成： 持久层的操作：

（对于 "select * from books")
Book.all

( 对于 "select * from books where author = '大刘' )
Book.where("name = '大刘'")


对于 `insert into books(title, author) values ("<<十万个为什么>>", "李博士")`
:

```
book = Book.new
book.title = "《十万个为什么》"
book.author = "李博士"
book.save
```

上面四行代码，可以简写为：

```
Book.create({ :title => '十万个为什么', :authro => '李博士'})
```

你会发现一个巨大的区别：

SQL语句的形式： 特别原始。 不好操作。不好维护。特别容易出错。
持久层的形式： 特别贴近于自然语言。 好维护。 好学习。 而且：菜鸟写的持久层操作， 都会被自动转换成高手写的SQL语句。


例如:

## 分页。

如果用MYSQL：
```
select ... from ... order by id limit(100) offset(2000)
```

oracle: 写法就变了：

```
select ... from ... order by id limit(100) top(2000)
```

ms sql server: 写法又变了：

```
select ... from ... order by id between 2000 and 2100
```

其他的： postgres, ... 都不一样。

所以，想兼容数据库，那就是一场噩梦。

（例如： mysql支持 `select ... from (select ... from ... )` 这样的嵌套 select,  其他好多数据库就不支持）

市面上， 根本找不到 熟悉所有数据库的人。 而且各个数据库的“方言(dialact )“ 都不一样。

十年以前的环境，比现在的还糟糕。（当年作java都没现在多。）

所以：持久层最大的卖点：

学好一个持久层， 可以操作所有数据库。

例如： 学好hibernate/Rails ActiveRecord, 可以在所有数据库上操作。
而且持久层生成的代码，就是专家级别的。(持久层在生成代码时会自动作优化。)

下面是持久层把 代码 转换成SQL语句的例子:

`City.first`:

```
SELECT  `cities`.* FROM `cities`   ORDER BY `cities`.`id` ASC LIMIT 1
```


`City.first.airport_managers`:

```
SELECT  `cities`.* FROM `cities`   ORDER BY `cities`.`id` ASC LIMIT 1
SELECT `airport_managers`.* FROM `airport_managers`  WHERE `airport_managers`.`city_id` = 1
```

`City.first.airport_managers.count`

```
SELECT  `cities`.* FROM `cities`   ORDER BY `cities`.`id` ASC LIMIT 1
SELECT COUNT(*) FROM `airport_managers`  WHERE `airport_managers`.`city_id` = 1
```

4. Rails的XX机制，让页面显示了持久层的数据。

CRUD:
先创建一个表：  `books`


4.1 新建一个migration
```
$ bundle exec rails g migration create_books
      invoke  active_record
      create    db/migrate/20160926041205_create_books.rb
```

4.2 为它增加内容:

```
def change
  create_table :books do |t|
    t.string :author
    t.string :title
    t.timestamp
  end
end

```

4.3 运行migrate:
```
$ bundle exec rake db:migrate
== 20160926041205 CreateBooks: migrating ======================================
-- create_table(:books)
   -> 0.0030s
== 20160926041205 CreateBooks: migrated (0.0031s) =============================
```

4.4 创建model:

```
# app/models/book.rb
class Book < ActiveRecord::Base
end
```

4.5 进入到控制台

(说明: 所有 `irb>` 开头的代码,都表示本行从 rails console中敲入 )

```
$ bundle exec rails console
```

就可以进入到控制台, 输入`Book.connection` 就可以连接并操作数据库了:

```
Loading development environment (Rails 4.1.6)
irb(main):001:0> Book
=> Book (call 'Book.connection' to establish a connection)
irb(main):002:0> Book.connection
```

接下来,我可以创建一个book, author为 王博士

```
irb> book = Book.create :author => '王博士'
```

```
sqlite> select * from books;
1|十万个为什么|王博士
```

```

irb>book = Book.first

irb>book.title = '二十万个为什么'
irb>book.save
```

可以看到, 上面的代码,被转换成了下面的SQL语句:
```
   (0.2ms)  begin transaction
  SQL (0.5ms)  UPDATE "books" SET "title" = ? WHERE "books"."id" = 1  [["title", "二十万个为什么"]]
   (173.7ms)  commit transaction
```


```
irb> book.delete
  SQL (52.4ms)  DELETE FROM "books" WHERE "books"."id" = 1
=> #<Book id: 1, title: "二十万个为什么", author: "王博士">
```

插入100条语句：

```
irb> (1..100).each  { |e|  Book.create :title => "#{e}个为什么" }
```

会有100条SQL被转换出来,并且被执行.

查询：

```
irb> Book.where('title like "5%"')
  Book Load (0.6ms)  SELECT "books".* FROM "books"  WHERE (title like "5%")
```

```
Book.where('title like "5%"').order('title desc') # 根据title倒序
```
TODO 补充


# 作业：

1. 在console下面， 实现book表的增删改查。
2. 在action里面， 实现 book表的增删改查。
