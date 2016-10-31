# 钩子方法 hook method（回调函数 callback）

我们在编程时，会发现有很多方法。

有几种方法，是比较特殊的方法。 它们存在的意义，往往跟过程相关。

## 实现模式 implementation pattern

找女人约会， 要记得买花， 这是模式
跟男朋友约会， 要记得打扮，也是妹子的模式。
陌生人头一次见面，要握手，这是模式。
就算不认识对方，但是也要给对方留余地， 往往会说： 久仰！ 幸会！

我们在编程的时候， 也要用到模式。 例如：

一个person，  能出生， 就一定会死亡。
```

class Person
  def born
  end

  def dead
  end
end
```

再比如：

```
class Money

  def get
  end

  def spend
  end

end
```

上面的方法， 是有对应的关系的，这个对应关系， 就是一种模式。

所以，好的代码，看起来应该是：

```
class MyModel

  def before_save
  end

  def save
  end

  def after_save
  end
end

```

我们接下来看这个代码：

```

public class MysqlDemo {
    public static void main(String[] args) throws Exception {

				// 这里就是 before_execute();
        Connection conn = null;
        String sql;
        String url = "jdbc:mysql://localhost:3306/javademo?"
                + "user=root&password=root&useUnicode=true&characterEncoding=UTF8";

				Class.forName("com.mysql.jdbc.Driver");// 动态加载mysql驱动

				System.out.println("成功加载MySQL驱动程序");
				// 一个Connection代表一个数据库连接
				conn = DriverManager.getConnection(url);
				// Statement里面带有很多方法，比如executeUpdate可以实现插入，更新和删除等
				Statement stmt = conn.createStatement();

				// 这里就是 execute()
				sql = "create table student(NO char(20),name varchar(20),primary key(NO))";
				int result = stmt.executeUpdate(sql);// executeUpdate语句会返回一个受影响的行数，如果返回-1就没有成功
				if (result != -1) {
						System.out.println("创建数据表成功");
						sql = "insert into student(NO,name) values('2012001','陶伟基')";
						result = stmt.executeUpdate(sql);
						sql = "insert into student(NO,name) values('2012002','周小俊')";
						result = stmt.executeUpdate(sql);
						sql = "select * from student";
						ResultSet rs = stmt.executeQuery(sql);// executeQuery会返回结果的集合，否则返回空值
						System.out.println("学号\t姓名");
						while (rs.next()) {
								System.out
												.println(rs.getString(1) + "\t" + rs.getString(2));// 入如果返回的是int类型可以用getInt()
						}
				}

				// after_execute();
				conn.close();

    }

}

```

所以，上面的代码就是三部分组成：

before_execute,
execute
after_execute

一旦写的代码多了，会发现，如果这个方法，(execute) 看成是一个东西的话，我们一把它从脑海里，拎出来，就会发现，
会出来三个：  before,  execute, after..
就好像 有钩子，勾在 execute这个核心方法上一样。

所以，我们管， before_execute, after_execute, 这样的方法，叫做： 钩子方法  (hook method)

我们写代码，写了 before， 就一定要写after. 哪怕，after中，是空代码，也要写出来。


## 回调函数。 callbacks.

我们要发起一个上传图片的http请求。 那么，这个请求，会有3种不同的后果：

1. success.
2. error
3. processing.

所以，我们往往会这样写：（使用了最常见的javascript代码）

```
$.ajax({
  url: '../wp-content/themes/bsj/php/validate.php',
  type: 'post',
  a: 1,
  success: function(){
    alert('success!');
  },
  error: function(){
    alert('error!');
  },
	processing: function(){
    alert('处理当中。。。')  // 这里用于： 显示上传进度。
	}
});
```

## 事件(events) 和 通知。

场景：

```
$('form').submit()

$('button').click(function(){
  alert('hi!')
})

```

## rails中的 hooks method.

controller中：


```
  def edit
    @article = Article.find params[:id]
  end

  def update
    @article = Article.find params[:id]
    redirect_to articles_path
  end

  def destroy
    @article = Article.find params[:id]
    @article.destroy

    redirect_to articles_path
  end
```

上面3行代码，都是一样的：

```
    @article = Article.find params[:id]
```

所以， rails 提供了 hook method:  `before_filter`


```

class XXAction
  before_action :get_article, :only => [:edit, :update, :destroy]

  def edit
  end

  def update
    redirect_to articles_path
  end

  def destroy
    @article.destroy

    redirect_to articles_path
  end

  private
  def get_article
    @article = Article.find params[:id]
  end

end
```

### ActiveRecord 中的 hooks.

```
class Count < ActiveRecord::Base

  before_save :say_hi

  # 这个方法是隐形的。 定义于： ActiveRecord::Base 中。
  def save
  end

  def say_hi
    puts "=== hihihi"
  end
end
```
