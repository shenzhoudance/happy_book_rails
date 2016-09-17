# 数据库关联


## 最简单的关联关系：  一对多。

王妈妈， 有两个孩子。小明 和 小亮。

可以说：  王妈妈 ， 有多个孩子。
也可以说，
小明， 有一个妈妈。
小王，有一个妈妈。

### 用数据库结构来表示

mothers
---------
id    name
1     王妈妈


children
----------
id    name
100   小王
101   小明

上面是不考虑表间关系的表。

那么， 如何在数据库中， 设计表间关系呢？  为某个相关的表，增加列。 （数据库么，就是保存数据的。
关系也是一种数据阿)

所以， 一对多关系， 要在  多的一端， 增加一列.

children
----------
id      name    mother_id
100     小王    1
101     小明    1

上面的 mother_id 列， 就是外键。(foreign key) 就是个名字而已，没有太大作用。
(表示： 该行， 对应 mother 表中的某个记录）

唯一作用就是： 记录了 表间关系。
外键的普通话翻译：  表示， 所在表的所在行， 属于其他某个表的 id = x 的记录。

### 用SQL来表示。

1


### 用代码来表示

class Mother
  has_many :sons
end

class Son
  belongs_to :mother
  # 完整形式是：
  belongs_to :mother, :class => 'Mother', :foreign_key => 'mother_id'
end

然后，我就可以：

xiao_wang = Son.first
# 就会生成SQL：   select * from sons where id = 1;
mama = xiao_wang.mother
# 就会生成SQL：
#   select * from mothers
#        join sons
#        on sons.mother_id = mothers.id
#        where sons.id = 1

Rails 是如何 根据 配置，来自动生成上面的复杂的 SQL 语句的？

```
  belongs_to :mother, :class => 'Mother', :foreign_key => 'mother_id'
```
1. belongs_to :mother, rails就能判断出：  mothers 表，是一的那一端。
而当前class 是： "class Son", 那么rails 就知道了 两个表的对应关系。
2. :class => 'Mother', 表示， 一的那一端， 对应的model class是Mother.
根据rails的惯例， Mother model对应的是 数据库中的 mothers 表。
3. :foreign_key => 'mother_id', rails就知道了， 外键是 'mother_id'.
而一对多关系中， 外键是保存在 多的那一端（也就是 sons)

所以， 这个复杂的SQL 条件就齐备了， 可以生成了。

所以，上面的ruby代码，配置好之后， 就可以这样调用：

Son.first.mother  # .mother方法， 作用在 son 上。 是有 class Son 中的 belongs_to 产生的。
Mother.first.sons   # .sons 方法， 作用在 mother上， 是由 class Mother 中的 hash_many  产生的。

## 一对一： 一对多的特例。

一个老婆， 有一个老公。

老婆：  有一个老公
老公：  有一个老婆。

mothers
--------------
id    name
1     王妈妈

fathers
--------------
id    name
200   李爸爸

那么，外键， 放在哪个表都可以。 (你可以在 mothers表，增加一个列， 叫father_id, 也可以在fathers表，增加
一个列， 叫mother_id )

## 多对多：

一个学生， 有多个老师 （ 学习了多门课程）
一个老师，可以教多个孩子 （教一门课程，但是有好多学生来听这个课程）


### 表结构

students
----------------
id    name
1     小王
2     小明
3     小红

teachers
----------------
id    name
100   王老师
200   李老师


目前看来， 把外键，放在任何一个表中都满足需求。 所以，需要中间表。  （课程）

lessons
----------------
id    name      student_id      teacher_id
1000  物理课    1(小王id)       100(王老师）
2000  物理课    2(小明id)       100(王老师）
3000  物理课    3(小红id)       100(王老师）
4000  化学课    1(小王id)       200(李老师）
5000  化学课    3(小红id)       200(李老师）

从上表中，可以看出，
王老师， 上的是物理课， 教了 3个孩子：  小王，小明和小红
李老师， 上的是化学课， 教了 2个孩子：  小王和小红。

### 用SQL来表示

小王都有哪些老师？ (一个SQL例子）

select teachers.*, students.*, lessons.*
    from lessons   //因为找的是老师，我们就要 from teachers ,

    join teachers
    on lessons.teacher_id = teachers.id  // 通过中间表，把老师 join 弄过来

    join students
    on lessons.student_id = students.id  // 通过中间表，把学生 join 弄过来

    where students.name = '小王'

这个 复杂的SQL 会生成下面的表：

teachers.id  teachers.name  students.id  students.name  lessons.id  lessons.name  lessons.student_id, l.t_id
------------------------------------------------
100             王老师        1             小王          1000      物理成绩        1                   100
100             王老师        2             小明          2000      物理成绩        2                   100
100             王老师        3             小红          3000      物理成绩        3                   100
200             李老师        1             小王          4000      化学成绩        1                   200
200             李老师        3             小王          5000      化学成绩        3                   200

跟下面的表是严格相对的：
id    name      student_id      teacher_id
1000  物理成绩    1(小王id)       100(王老师）
2000  物理成绩    2(小明id)       100(王老师）
3000  物理成绩    3(小红id)       100(王老师）
4000  化学成绩    1(小王id)       200(李老师）
5000  化学成绩    3(小红id)       200(李老师）

### 用代码来表示

class Student
  has_many :lessons
  has_many :teachers, :through => :lessons
  # 上面的简写， 相当于：
  has_many :teachers, :class => 'Teacher', :foreign_key => 'teacher_id', :throught => :lessons
end

class Teachers
  has_many :lessons
  has_many :students, :through => :lessons
end

上面的代码定义完之后，就可以实现这个了：

小王都有哪些老师？ (同SQL例子）
Student.find_by_name('小王').teachers

(如果你不需要 查询： 王老师，有哪些学生？， 就不需要定义 class Teacher里的  has_many :students )
所以说，rails中的定义，非常灵活。 但是， 实战中， 建议都老老实实的加上。 这样
当你的同事， 如果之前哪怕不知道有student这个model, 但是，看到了teacher这个model,
也就直到了teacher与student的关系了。

另外，从实现模式的角度讲， 也要两端都加上这个has_many 的声明。
(什么时实现模式呢？
我们连接数据库：

有
connection.start
就要有
connection.close

对于一些回调函数, 有before, 就要有after
对于一些回调函数, 有success, 就要有error/failer:
)

### 注意： 多对多关联， 不要学习： has_many_and_belongs_to
为什么？
因为，这个方法，会生成一个无意义的只包含外键的中间表。  例如：

表名： student_teachers
student_id    teacher_id
--------------------------
1             100
2             100
3             100

不建议。
1. 表名不明确。 不要使用 a_bs 这样的表名。对应model比较难写。 (app/models/a_b.rb 吗？)
2. 任何一个中间表，都是有意义的。 90%的时候，中间表， 是有正常的列的。与其后期通过migration加上这个列， 不如
一开始，就不要使用 hmabl 这样的方式来声明（声明之后， model 的名字就定下来了。难改）
