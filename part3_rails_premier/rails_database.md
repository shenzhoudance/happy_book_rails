rails数据库迁移和model验证

一.rails 数据库迁移

由于需求变动，功能增加或者系统架构调整等种种原因，我们需要经常性的做数据库的迁移操作。在rails框架下，我们的数据库迁移工作会
由Active Record提供的一个功能，按照时间的顺序来管理数据库。在rails中，使用迁移我们无需编写SQL语句，只需要使用简单的Ruby DSL就能
够修改数据表，下面就是对Rails数据库迁移操作的一些简单介绍。

1.迁移简介

　　迁移使用一种简单且统一的方式来按照时间的先后顺序来修改我们的数据库。迁移使用Ruby DSL语言来完成，所以并不需要我们手动编写SQL语言去完成
对于我们使用的数据库种类和操作数据库的方式并不关心。你可以把每一次迁移看做一次数据库版本的改变。一开始的数据库什么都没有，每一次迁移我们都会
插入，修改或者删除其中的一些表，列或者字段。Active Record 知道怎样按照时间线来更新你的数据库，无论当前的数据库是什么状态，都能够更新到最新的
版本状态。Active Record 同时会更新你的 db/schema.rb文件匹配到最新的数据库结构。下面是一个数据库的例子：

创建好一个应用程序之后，执行下面的命令

   $ rake db:create 创建新的初始数据库

   $ rake db:migrate 执行数据库的初始迁移往数据库中增加初始的数据

2.迁移生成

  迁移会创建一个单独的文件存放在 db/migrate 目录下，每次迁移都会保存在一个独立的文件中。文件名的格式为：时间戳＋下划线分割的迁移文件名。时间戳的
格式为YYMMDDHHMMSS。下划线分割的迁移文件名要和迁移文件的类名相匹配，例如在20150901120000_create_products.rb文件中必须有一个类名为createProduct的类
，在20150901120001_add_details_to_products.rb文件中必须定义一个addDetailsToProducts的类。文件执行的先后顺序以及执行哪个迁移由时间戳来决定。这个在自
己生成迁移文件或者拷贝别的文件中的迁移文件时需要特别注意。

  2.1创建迁移
  自己计算时间戳并不是一件简单的事，好在rails的Active Record提供了一个自身的生成器，可以帮助我们来完成这个工作。
```ruby
  $ rails generate migration AddPriceToProducts 这个命令会生成一个空的迁移文件，如下所示
  class AddPriceToProducts < ActiveRecord::Migration
    def change
    end
  end
```
  如果迁移的名字是AddXXXToYYY或者RemoveAAAFromBBB，并且后面跟着一个字段名和数据类型，那么生成的迁移中会包含合适的add_column和remove_column语句，如下所示：

```ruby
$ rails generate migration AddPriceToProducts price:string
生成的迁移文件：
class AddPriceToProducts < ActiveRecord::Migration
  def change
    add_column :products, :price, :string
  end
end
```
 如果我们要为刚才增加的字段添加索引，可以使用如下的命令
```ruby
$ rails generate migration AddPriceToProducts price :string :index
生成迁移文件
class AddPriceToProducts < ActiveRecord::Migration
  def change
    add_column :products, :price, :string
    add_index :products, :price
  end
end
```
以此类推，我们也可以创建一个删除字段的迁移文件
```ruby
$ rails generate migration RemovePriceFromProducts price:string
生成的迁移文件：
class  RemovePriceFromProducts < ActiveRecord::Migration
  def change
    Remove_column :products, :price, :string
  end
end
```
虽然上面只是创建字段的例子，但是我们迁移生成器绝不是只能修改字段，如果我们使用Create命令，我们就能生成一个表。

具体的命令格式:$ rails generate migrate CreateXXX 后面跟上所需要的字段和字段类型，具体的例子如下所示：
```ruby
$ rails generate migration CreateUsers name:string password:string email:string
生成的迁移文件如下：
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :name
      t.string :password
      t.string :email
    end
  end
end
```
Rails自身的生成器只能帮助我们生成一些基础的代码，我们可以根据自己的需要修改其中的代码，来实现自己的需求。

2.2编写迁移

  2.2.1创建数据表
 基本上我们每次都会Rails自带的生成器产生基础的数据表迁移文件，如下所示：
```ruby
    create_table :users do |t|
      t.string :name
      t.string :password
      t.string :email
    end
```

这是一个基本的数据表产生文件，默认情况下，create_table方法会生成名字为id的主键，我们通过:primary_key选项可以修改主键名
（需要注意的时我们需要同时修改相应的model）。如果有些人不希望生成主键，我们可以使用id: false选项来控制它。如果设置数
据库的选项，我们可以使用:options选择中使用SQL。

2.2.2创建联结表

创建联结表的时候我们会使用create_join_table方法来创建HABTM数据表。一个典型的例子如下所示：
```
create_join_table :products, :categories
```
这个方法会创建一个拥有catagories_id和products_id两个字段的categories_products数据表。这两个字段的:null选项默认情况下都是false
,同样这个也可以在:column_options选项中设置。
```
create_join_table :products, :categories, table_name: :categorization
column_options: {null :true}
```
上面的代码同样会改变数据表的名字为categorization.
create_join_table同样可以接受代码块，你同样可以增加索引（默认情况下没有）或者其他的一些字段。
```ruby
create_join_table :products, :categories do |t|
  t.index :product_id;
  t.index :category_id;
end
```
2.2.3修改数据表
有一个和create_table类似的方法change_table,该方法可以用来修改已经创建好的数据表。使用该方法和使用create_table方法相类似，只不过
传入块中的参数需要更多的技巧。
```ruby
change_table :products do |t|
    t.remove :description, :name
    t.string :part_number
    t.index :part_number
    t.rename :upccode, :upc_code
end
```
这个方法移除description和name字段，增加了part_number字段，重命名了upccode字段为upc_code字段。

2.2.4其他数据库迁移方法 （这些方法以后在讲）

　１.change方法
     change方法是迁移中最常使用的方法，能够完成大部分的操作，而且Active Record知道如何撤销这些操作。在change方法中，目前有这些常用的
方法：　add_column  add_index add_reference add_timestamps  create_table  create_join_table  drop_table(需要代码块)　drop_join_table(需要代码块)
remove_timestamps  remove_column  remove_index  remove_table  remove_reference
　２.reversible方法
在我们需要撤销复杂的迁移操作时，我们可以使用reversible方法来完成这些操作。
　３.up和down方法
　４.revert方法

3.运行迁移

Rails拥有一整套的执行迁移任务的方法。
我们最常用的是其中的rake db:migrate，使用该方法我们可以完成基本所有的数据迁移工作，他是按照时间戳的先后顺序来执行的。同时，执行该
任务的同时也会执行db:schema:dump,更新db/schema.rb文件，来匹配最新的数据库结构。如果指定了特定的版本，Active Record 会执行该版本之前
的所有迁移，但不会执行该版本之后的迁移文件，所谓的版本就是迁移文件前面的数字部分，我理解的也就是迁移文件前面的时间戳。例如我们要执行
20150902120000这个文件我们只需要输入下面的命令：
$ rake db:migrate VERSION=20150902120000

3.1迁移回滚

这里我们还有一个常用的操作命令就是当我们的迁移代码写错了，需要纠正的时候，我们执行下面的回滚操作
$ rake db:rollback
这个命令就会帮助我们回滚到上一次迁移的状态，如果我们要回滚到多个迁移之前，我们需要使用SETP参数，执行　$ rake db:rollback STEP=3
这个命令会帮助我们回滚到３次迁移前的状态。
还有一个类似的回滚的操作方法$ rake db:redo 该方法的执行和rollback方法完全类似，同样可以使用SETP参数来指定回滚的次数。
然而这些方法我们都可以使用$ rake db:migrate　命令来实现，只是上面的方法可以更加方便，migrate方法需要我们找到回滚的目标状态的版本号，
这些方法并不需要。

3.2搭建数据库

$ rake db:setup 命令会帮助我们重建数据库，加载模式，填充原始数据。
3.3重建数据库

$ rake db:reset 该命令会帮助我们删除数据库然后在重建，等价于　rake db:drop db:setup
需要注意的是该方法使用的时db/schema.rb文件中的内容，如果迁移无法回滚，那么我们就不能完成该命令。
3.4还有一些其他的迁移方法，暂时觉得不是很常用，以后用到的时候在补充。

４.迁移和种子数据

很多人使用迁移往数据库中增加数据，类似下面的代码：
```ruby
class AddInitialProducts < ActiveRecord::Migration
def up
  5.times do |i|
  Product.create(name: "Product ##{i}", description: "A product.")
  end
end

def down
  Product.delete_all
  end
end
```
然而我们完全可以使用Rails 提供的seed功能，可以把数据存入数据库。这个功能的使用很简单，在db/seeds.rb文件中写入一些ruby代码，然后执行　rake db:seed命令即可。
db/seeds.rb文件中的内容如下：
```ruby
  5.times do |i|
  Product.create(name: "Product ##{i}", description: "A product.")
  end
```
这种方法使用起来相比于第一种方法更加的方便快捷。

二.model验证(数据验证)

1.数据验证

　1.1为什么要做数据验证？
     数据验证能够保证存入数据库的数据都是合法而且有效的。因为我们的程序经常需要用户提供合法的Email地址和手机号码之类的，所以我们要确保得到的数据都是可用合法的,
所以我们需要做数据验证。通常在模型中的数据验证时最为有效的，只有通过验证的数据才能够存入数据库中。数据验证和使用的数据库种类无关，终端用户也无法跳过数据验证，并且
代码也更便于维护和修改。Rails中的数据验证很简单，Rails内置了很多帮助我们完成验证的方法。

　1.2什么时候做数据验证？
    在Active Record中数据对象有两种状态，一种是已经在数据库中的数据对象，一种则是没有。一般而言，新建的数据都是不属于数据库中的数据对象，在该对象调用save方法之后，
才会被存入数据库中。数据验证就是发生在调用save方法之后，执行数据写入数据库之前的时候，如果验证通过则存入数据库，验证失败对象会被标记为不合法，Active Record不会
执行将对象写入数据库的操作。下面常用的rails方法都会做数据验证 create !create save !save update !update

  1.3跳过验证
 　 下面的方法则会跳过数据验证，使用这些方法的时候我们需要特别留意。
 decrement!  decrement_counter increment! increment_counter toggle! touch update_all update_attribute update_column updata_columns  update_counters
 有一种情况就是当我们使用save时传入validate: false,同样会跳过验证，应该引起我们的注意 　save(validate: false)

 1.4 valid?和invalid?
Rails 使用vaild?方法来检查对象是否合法。valid?方法会触发数据验证，对象合法返回true,否则返回false。invalid?方法时valid?方法的逆测试，如果合法返回false,否则返回true。

 1.5 errors[]
 要检查对象的某个属性是否合法，我们可以使用errors[:attribute],errors[:attribute]中包含有:attribute的所有错误，如果某个属性没有错误，就会返回空数组。
但是该方法只在数据验证之后起作用，因为它只能用来收集错误信息，并不能触发数据验证，而且该方法不能验证整个对象，他只能验证该对象的某个属是否出错。

2.数据验证帮助方法
    Rails有很多预先定义好的数据验证的帮助方法，我们可以直接在模型类的定义中使用，这些帮助方法提供了常用的验证规则。每次验证失败后，都会向对象的errors
集合中添加一个错误的消息，这些消息和所验证的属性是相关联的。常用的验证方法如下所示(这里我只做了大概的方法说明，具体的使用还是需要自己去验证)
　 2.1acceptance
    检查表单提交时，用户界面的复选框是否被选中，这种验证一般只是针对网页程序，并不会存入数据库。
   2.2validates_associated
   用来验证模型之间的相互关联性，保存对象时，该方法会在相关联的每个对象上都调用valid?方法。但是需要注意的是，不能在关联的两端同时使用validate_associated，因为这样会形成
一个循环验证。
　 2.3confirmation
验证两个文本字段的值是否完全相同，例如确认密码就可以调用该方法。
　 2.4exclusion
这个帮助方法用来验证属性的值是否不在指定的集合中，集合是一个可以枚举的对象。
其他方法:   format 检查属性的值是否匹配　inclusion检查属性的值是否在指定的集合中，集合可以是任何一个可枚举的对象。　length验证对象的长度是否符合模型的规定。该方法拥有如下的限制选项
:minimum　：属性的值的长度不能小于指定的长度　:maximum　：属性的长度不能比特定的长度长　:in(或者:within):属性值的长度在给定的数值之间　:is :属性的长度必须等于给定的值。
numericality 检查属性的值是否包含数字，默认匹配的值正负整数或浮点数。如果我们只需要整数可以设置:only_integer选项为true。 presence　检查指定的属性是否非空。absence　检查指定的属性
是否为空。　uniqueness　检查对象的属性值是否唯一。　validates_with　该方法将记录交给其他的类来做验证。 validates_each 该方法把属性的值传入代码库中做检查，没有预先定义的验证方式，
我们需要在代码库中定义验证方法。

3 常用的验证选项

　3.1:allow_nil 当指定:allow_nil 选项后，如果验证的值为nil就会跳过验证。

　3.2:allow_blank 与上一个方法类似，如果验证的值为空时，就会自动跳过验证

　3.3:message 如果验证失败，会把:message选项指定的字符串添加到errors集合中。如果没有指定该选项，Active Record会使用各种验证帮助方法的默认错误消息。

　3.4:on　用来指定什么时候做验证。

4 严格验证

　数据验证的严格模式在验证失败时会抛出ActiveModel::StrictValidationFailed异常。　通过使用:strict选项，我们还可以指定抛出什么异常。
除此之外，还有条件验证,自定义验证等，条件验证通过使用:if 和 :unless选项来实现，这两个选项可以实现多种的条件验证，以后再讲。自定义验证就是用户自己可以定义验证使用的方法或类，完成
Rails自己的内建帮助验证方法不能满足的验证需求。

总之，数据验证是一个非常庞杂且重要的事情，在这里由于个人水平和时间问题，我不能把这部分十分透彻的说清楚，还请大家多多指正。

