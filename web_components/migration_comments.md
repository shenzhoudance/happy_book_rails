# migration comments

如果我们的命名良好时，不需要为数据库的某个列，某个表增加注释。

但是实践证明，大部分的国内开发者都无法100%的保证自己的名字是良好的，
例如：'客厅', '客餐厅',
所以，我们须要为每个列都增加注释。

这个 migration comments gem就是为了解决MYSQL 下这个问题的。

## 使用方法：

```
# Gemfile
gem 'migration_comments'
```

注意：

- 所有的注释，都添加在migration中!
- 为列增加注释时，最好加上个例子。例如：  "该列保存用户的年纪，例如： 32"

## 为表和列增加注释

```ruby
def self.up
  set_table_comment :users, "用户表"
  set_column_comment :users, :email, "用户的邮箱，例如： a@b.com"
end
```

也可以单独指定 comment:
```ruby
def self.up
  change_table :users do |t|
    t.comment "普通用户表。"
    t.change_comment :email, "用户的邮箱，必须是happysoft.com的，例如：a@happysoft.cc"
  end
end
```

在新建table时指定comment:
```ruby
def self.up
  create_table :users, :comment => "用户表" do |t|
    t.string :email, :comment => "email, 例如： a@b.com"
  end
end
```


删除表或者列中的comment:
```ruby
def self.up
  remove_table_comment :table_name
  remove_column_comment :table_name, :column_name
end
```

把所有的列子综合起来：
```ruby
def self.up
  change_table :existing_table do |t|
    # 删除某个表的comment
    t.comment nil
    # 增加一个新列.带有comment.
    t.string :new_column, :comment => "新列的comment"
    # 删掉一个列的注释
    t.change_comment :existing_column, nil
    # 修改一个列的类型, 连带注释也删掉。
    t.integer :another_existing_column, :comment => nil
    # 仅仅修改列，不修改它的注释
    t.boolean :column_with_comment
  end
end
```
