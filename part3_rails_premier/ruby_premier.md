#Ruby入门

## Ruby简介

Ruby 是脚本语言，由日本人松本行弘编写。这个语言最大的特点就是＂对程序员友好＂．
用Martin Fowler等人的话说，就是＂我在任何时候手头都要带着它（Ruby锄头书）＂

如果你接触过C/C++, java, object C等语言，回过头再看Ruby，就会发现它特别简洁，代码极度好懂．

Ruby支持Linux, Mac, 以及Windows. 但是我们只在Linux 和Mac下工作，Windows我们就不考虑了．

下面都假设你在Linux下(大部分情况适用于Mac)

## 安装Ruby

虽然系统默认自带了Ruby，但是不如我们自定义的灵活．我们使用[rbenv](https://github.com/sstephenson/rbenv) 来安装Ruby,　最大的好处是
可以允许你同时安装多个Ruby版本．


### 安装rbenv
rbenv(ruby environment),是管理多个不同版本的ruby工具，是之前rvm(ruby version manager)的替代品。类似的工具还有管理不同Node版本的nvm(node version manager)。

具体的安装步骤为:

```
git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
# 用来编译安装 ruby
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
# 用来管理 gemset, 可选, 因为有 bundler 也没什么必要
git clone git://github.com/jamis/rbenv-gemset.git  ~/.rbenv/plugins/rbenv-gemset
# 通过 gem 命令安装完 gem 后无需手动输入 rbenv rehash 命令, 推荐
git clone git://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash
# 通过 rbenv update 命令来更新 rbenv 以及所有插件, 推荐
git clone https://github.com/rkh/rbenv-update.git ~/.rbenv/plugins/rbenv-update
```

然后把下面的代码放在~/.zshrc里面
```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

*注意* :如果你使用的是bash，那应该把上面的代码放在~/.bashrc或者~/.bash_profile里面

最后，重启一下你的Terminal就OK了。

### 安装ruby
```
rbenv install --list # 列出所有可以安装的ruby版本
rbenv install 1.9.3-p551 # 安装特定版本的ruby
```
*注意* :使用rbenv安装了ruby之后，要指定你想要使用的本班为当前版本
```
rbenv global 1.9.3-p551 # 指定版本
```
### 列出ruby版本
```
rbenv versions # 系统所有通过rbenv安装的ruby版本
rbenv version # 当前版本
```


## 调试方法: irb

irb = interactive ruby

在命令行下，输入 `$ irb` 即可．　这是一个ruby的即时调试界面．

### 几点说明

用 `# => `表示输出结果 , 例如：

```ruby
puts "goodbye java"
# => "goodbye java"
```

用$, 或者# 表示bash命令行程序，例如：

```bash
$ ruby test_my_module.rb
```
或者：
```ruby
# ps -ef | grep nginx
```


## Hello World

新建一个文件
```ruby
# hello.rb:
puts 'Hello world!'
```

运行，就会看到效果：

```bash
$ ruby hello.rb
```

## 赋值

```ruby
name = "jim"
# => jim
```

## 字符串插值

在Ruby中，我们最好所有的地方都用字符串插值(interpolition) ,避免使用 +　方式．
原因是：对于不同的数据类型，用＋号时，系统会报错．

使用字符串插值时，记得用双引号．

```ruby
puts "hi, #{name}!"
# => hi, jim
```

你也可以把它写成：

```ruby
puts "hi, "+ name
# => hi, jim
```

如果用加号，一些时候就会报错：

```ruby
a = 1
puts "a is: " + a
# => 报错

puts "a is: #{a}"
# => a is: 1
```

## 双引号与单引号

## class

我们从一个例子来看,　注意代码中的注释：

```ruby
class Apple

  # 这个方法就是在　Apple.new　时自动调用的方法
  def initialize
    # instance variable, 实例变量
    @color
  end


  # getter 方法
  def color
    return @color
  end

  # setter 方法
  def color= color
    @color = color
  end

  # private 下面的方法都是私有方法
  private
  def i_am_private
  end
end

red_apple = Apple.new
red_apple.color = 'red'
puts "red_apple.color: #{red_apple.color}"
```

我们来运行这个文件：
```bash
$ ruby apple.rb
# => "red_apple.color: red"
```

上面的例子是java/c风格的. ruby的熟手一般这么写：

```ruby
class Apple
  # 这一句，自动声明了 @color, getter,setter
  attr_accessor 'color'
end
```

## 变量

类变量：　class variable, 例如： `@@name`, 作用域：所有的多个instance 会共享这个变量. 用的很少．

实例变量　instance variable, 例如： `@color`, 作用域仅在instance之内

普通变量： local variable, 例如：　`age = 20`, 作用域仅在　某个方法内．

全局变量： global variable，　例如：`$name = "Jim"`, 作用域在全局． 用的更少．

下面是一个例子：

```ruby
class Apple

  @@from = 'China'

  def color = color
    @color = color
  end

  def color
    return @color
  end

  def get_from
    @@from
  end

  def set_from from
    @@from = from
  end
end

red_one = Apple.new
red_one.color = 'red'
puts red_one.color
# => 'red'
red_one.set_from 'Japan'
puts red_one.get_from
# => 'Japan'

green_one = Apple.new
green_one.color = 'green'
puts green_one.color
# => 'green'
puts green_one.get_from
# => 'Japan'
```

## 方法：类方法(class method)与实例方法(instance method)

用法上,　看这个例子:

```ruby
class Apple
  def Apple.name
    'apple'
  end
  def color
    'red'
  end
end

Apple.new.color
# => red

Apple.name
# => apple
```

## Symbol

前面的apple.rb例子中，　正常的应该写成：

```ruby
class Apple
  attr_accessor :color
end
```

这个　:color 是什么呢？　就是Symbol. 它是不会变化的字符串．
TODO　具体的学术概念

:name 等同于：　"name".to_symbol

## hash

```Ruby
# 任何情况下都生效的语法： =>
jim = {
    :name => 'Jim',
    :age => 20
}

# Ruby 1.9之后产生的语法：更加简洁．
jim = {
    name: 'Jim',
    age: 20
}

# 也可以写成：
jim = {}
jim[:name] = 'Jim'
jim[:age] = 20
```

但是，　symbol 与 string , 是不同的key,　例如：
```bash
a = {:name => 'jim', 'name'=> 'hi'}
a[:name]  #=>  'jim'
a['name'] #=>  'hi'
```

## ruby中的简写

### 每个函数的最后一行默认是返回值，是不需要写return的，例如：

```ruby
def color
  'red'
end
```

### 方法调用的最外面的大括号可以省略。例如：

```ruby
puts "hihihi"   # 等同于   puts("hihihi")
```

### hash最外面的{} 在大多数情况下可以省略，例如：

```ruby
Apple.create :name => 'apple', :color => 'red'

#等同于：
Apple.create({:name => 'apple', :color => 'red'})

#等同于： Apple.create name: 'apple', color: 'red'
```

### 调用某个block中的某个方法：

```ruby
Apple.all.each do {  |apple|      apple.name  }

# 等同于：
Apple.all.map(&:name)
```

## 条件语句

### if else end 是最常见的

```ruby
a = 1
if a == 1
  puts "a is 1"
elsif a == 2
  puts "a is 2"
else
  puts "a is not in [1,2]"
end
```

### case when end 分支语句

例如：

```ruby
a = 1
case a
  when 1 then puts "a is 1"
  when 2 then puts "a is 2"
  when 3,4,5 then puts "a is in [3,4,5]"
  else puts "a is not in [1,2,3,4,5]"
end
```

### 三元表达式

```ruby
a = 1
puts a == 1 ? 'one' : 'not one'
# => one
```

也可以写成：


## for, each, loop, while 循环

for 与each　几乎一样．例如：

```ruby
[1,2,3].each { |e|
  puts e
}

# 等同于下方：　
for e in [1,2,3]
  puts e
end
```

for 与 each 都可以做循环，但是高手都用each. 区别在于：for 是关键字，　each是方法.
for 后面的变量，是全局变量，不仅仅存在于for .. end 这个作用域之内．(具体见这个stackoverflow
上的问题: for vs each.)[http://stackoverflow.com/questions/3294509/for-vs-each-in-ruby])

举个例子：

```ruby
for i in [1,2,3]
  puts i
end
puts i  # => 3
```

loop与while是几乎一样的.

```ruby
loop do
  # your code
  break if <condition>
end

begin
  # your code
end while <condition>
```
但是ruby的作者推荐使用loop. 因为可读性更强． 下面是一个例子：

```ruby
a = [2,1,0,-1,-2]
loop do
  current_element = a.pop
  puts current_element
  break if current_element < 0
end

# => 2
# => 1
# => 0
```


## 命名规则

常量: 全都是大写字母．`ANDROID_SYSTEM = 'android'`

变量：如果不算@, @@, $的话，是小写字母开头．下划线拼接．例如: `color`, `age`, `is_created`

class, module: 首字母大写，骆驼表达法： Apple, Human

方法名：　小写字母开头．　可以以问号？ 或者等号结尾，例如： `name`, `created?`, `color=`

<<Metaprogramming Ruby>>:
> Ruby高手都用 each 循环．我们跟着照做就好．

## 循环

### for 循环


## 查看API

查看ruby API　很其他的语言差不多．官方文档是：api.ruby-lang.org?
TODO 详细图文

## ruby DEBUG　之调试信息

- ruby的出错信息, 距离顺序是从上到下，时间顺序是从下到上，出现的．例如：

```ruby
test_class.rb:8:in `extend': wrong argument type Class (expected Module) (TypeError)
  from test_class.rb:8:in `<class:Child>'
  from test_class.rb:7:in `<main>'
```
上面信息中，时间的运行顺序是，先运行　test_class.rb的第７行，再运行到第8行，才出错．
出错信息是  `wrong argument type Class (expected Module) (TypeError)`

- 在调试中，class instance 的最外层是#<> 的固定格式，前面的Apple表示class名字，0x00000001f0dad8　是内存的地址．
例如：#<Apple:0x00000001f0dad8>


