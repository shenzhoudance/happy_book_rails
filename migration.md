# migration 数据迁移

migration, 是 DB migration的缩写。 这要写起来的话，也是厚厚的一本书。


## 为什么要使用 migration?

我们修改DB的时候，原始的办法：

1. 手动修改数据库的表Users，增加一列  name
2. 回来修改ruby代码，  User.first.name

但是，你的工作伙伴（工友们），如何知道，你为某个数据库的表，增加了一列呢？
难道你要求他们，也在自己的机器上做这个事儿吗？人肉做。

所以，我们要把上面的过程，自动化，可以复现。

所以：

使用统一的sql ， 某个人每次修改数据库表之后，都要更新这个sql文件。
但是有缺点： 测试数据很宝贵，很多测试数据，是非常复杂的，例如：有5个表，每个表之间都有
互相的依赖关系。 那么，一旦你的表结构变化，重新导入sql文件后，测试数据都没有了。

所以，我们要使用ruby代码，来完成这个事儿。

这个ruby代码，就是migration.

```
class CreateBooks < ActiveRecord::Migration
  def self.up
    create_table :books do |t|
      t.string :title

      t.timestamps
    end
  end

  def self.down
    drop_table :books
  end
end
```

上面代码，创建了一个migration, 每个migration, 都有2个方法.

- up
- down

为啥叫迁移？  因为它可以移过来，再移回去。

up ：  就是从过去，往未来的时间方向上发展
down:  在时间上倒退。


```
  20141207125533_create_sorts.rb
  20141208073607_add_logo_to_sorts.rb
  20141209021200_add_parent_id_to_sorts.rb
  20141209063659_create_pages.rb
  20141210111312_create_ckeditor_assets.rb
  20150131012039_add_position_and_is_visible_to_sorts.rb
  20150131015824_create_system_settings.rb
  20150131021810_add_instruction_to_system_settings.rb
  20150201013321_add_is_deletable_to_sorts.rb
  20150316021041_create_feedbacks.rb
  20150529075541_create_markets.rb
  20150529075608_create_companies.rb
  20150529080128_create_price_strategies.rb
  20150529080631_create_market_modules.rb
  20150529080834_create_download_counts.rb
  20150529092141_create_positions.rb
  20150601090602_add_name_to_price_strategies.rb
```

所以，当团队中，任意一个新手，加入的话，不需要你提供给他任何sql文件。让他直接运行
` rake db:migrate `就可以了。

我也可以使用 rake db:rollback 回退到任意时刻。

所以，可以认为，migration 是衡量一个项目的水平的重要指标。 如果一个项目，没有migration的话
那么，这个项目就特别难于开发：

- 数据库结构难以获取
- 开发成员之间的表结构难以统一。


## 使用原则

记得：

- 任何对数据库的操作（改变表的结构），必须使用migration
  - 创建表
  - 修改表
  - 删除表
  - 新增列
  - 修改列
  - 删除列
- migration 一旦创建好，并且上传到了远程服务器，就绝对不能做改动。

例如：我想新建一个表users, 它有个属性： name, age

```
$ bundle exec rails g migration create_users
      invoke  active_record
      create    db/migrate/20160308125025_create_users.rb
```

```
class CreateUsers < ActiveRecord::Migration
  def self.up

    create_table :users do |t|
      t.string :name
      t.integer :age

      t.timestamps
    end
  end

  def self.down
    drop_table :books
  end
end
```

( 如果你用rails 4.x 来创建的话，得到的migration ,一般没有up, down 方法。因为，rails
非常智能，能自动的，把 up, down方法合并成： change )

例如，如果我的up 方法中，是create_table, add_column, 那么，rails就会自动判断出，在down
 方法中，就用  drop_table, remove_column 。

但是，还是有些时刻，rails无法自动判断up/down, 例如： 改变某个列的类型。这个时候，还的
使用经典的 up/down方法。

运行 rake db:migrate:

```
$ bundle exec rake db:migrate
== 20160308125025 CreateUsers: migrating ======================================
-- create_table(:users)
   -> 0.0078s
== 20160308125025 CreateUsers: migrated (0.0079s) =============================
```

所以， 使用 sqlite3 命令，打开这个数据库，发现，数据库中，新增了一个表 users.
```
sqlite> .tables
books              schema_migrations  users
```

## 回滚

```
$ bundle exec rake db:rollback
== 20160308125025 CreateUsers: reverting ======================================
-- drop_table(:users)
   -> 0.0012s
== 20160308125025 CreateUsers: reverted (0.0068s) =============================
```

可以看到，table中就少了：users

```
sqlite> .tables
books              schema_migrations
```

那么，剩余表中有个表，比较奇怪：  schema_migrations, 这个是干嘛的呢？

## schema_migrations

专门记录当前数据库的 迁移 ID 是多少。 rails就是通过比较它和 db/migrate 中文件的差异，
来判断，当前的rails，的数据库，是否是最新的。

运行 create users 这个migration之前：
```
sqlite> select * from schema_migrations;
20151023070737
```

运行完之后：

```
sqlite> select * from schema_migrations;
20151023070737
20160308125025
```

多出来的一行：  "20160308125025" , 刚好，就是我们新建的migration :
`20160308125025_create_users`名字的一部分。


## 如何修改一个列？

错误的做法：

1. rake db:rollback
2. 修改 20160308125025_create_users 这个rb 文件的内容。

绝对错误！因为，记住： 一旦创建好的migration文件（特别是已经提交到了远程的话），就绝对
不要去修改它！

正确的做法是：

1. 新建个migration
2. 运行它


例如，我想把  age 列，改名字，改成： nian_ling,   我应该：

- bundle exec rails g migration rename_age_to_nian_ling_from_users_table
  - 这里的migration的命名，不是特别严格的。不会引起错误。但是原则上，migration 要：
看到名字，就能知道它是做什么的。 例如： createxxx,  remove_xxx_from_yyy,
  ```
  $ bundle exec rails g migration rename_age_to_nian_ling_from_users
        invoke  active_record
        create    db/migrate/20160308130535_rename_age_to_nian_ling_from_users.rb
  ```
- 再编辑上面命令，产生的文件。
  ```
  class RenameAgeToNianLingFromUsers < ActiveRecord::Migration
    def change
      # 下面这句是新增的
      rename_column :users, :age, :nian_ling
    end
  end
  ```
- 再运行 rake db:migrate
  ```
$ bundle exec rake db:migrate
== 20160308130535 RenameAgeToNianLingFromUsers: migrating =====================
-- rename_column(:users, :age, :nian_ling)
   -> 0.0111s
== 20160308130535 RenameAgeToNianLingFromUsers: migrated (0.0112s) ============
  ```

就能够看到，
```
sqlite> select * from schema_migrations;
20151023070737
20160308125025
20160308130535
```

并且， users中，age列变成了 nian_ling列。

```
sqlite> .schema
...
CREATE TABLE "users" ("id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, "name" varchar(255), "nian_ling" integer, "created_at" datetime, "updated_at" datetime);
sqlite>

```
