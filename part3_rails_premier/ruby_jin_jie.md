# Ruby 进阶

## Module

它用来把各种方法提炼出来,供其他 class 引用.

1. 不能被new
2. 可以被include
3. module中的 self.xx方法可以被直接调用
4. module中的普通方法,需要被某个class include之后, 由对应的class调用.


下面是一个module的例子:
```ruby
# tool.rb

module Tool

  # 这个方法无法直接运行.
  def show_tip
    "this is the tip"
  end

  # 这个方法可以通过 Tool.say 来运行,
  def self.say
    "hi"
  end
end

Tool.say   #=>  "hi", 这个时候,Tool.say就是一个class method.
Tool.new.show_tip  #=> 会报错  module.rb:11:in `<main>': undefined method `new' for Tool:Module (NoMethodError)
```

## Mixed In:  可以被作为mixed in 使用.

mixed in 是一种比 继承 和 interface/implementation 更加优秀的存在. 非常直观, 而且很好的重用了代码.

我们来定义两个class,  Student 和 Teacher. 下面可以看到, 这两个类,通过include, 都继承了 Tool module的方法: show_tip:

```ruby
require './tool.rb'

class Student
  include Tool
end

Student.new.show_tip  # => "this is the tip"

class Teacher
  include Tool
end

Teacher.new.show_tip  # => "this is the tip"
```

不要小瞧这个能力, 它用非常简洁的方式, 完成了其他语言难以完成的事情.

## 元编程初探

元编程, meta programming, 是一种特别强大的功能. 举个例子:

```ruby
def say_hi_to_jim
  "hi, jim"
end
def say_hi_to_li_lei
  "hi, li_lei"
end
def say_hi_to_han_mei_mei
  "hi, han_mei_mei"
end
```

这三个方法, 也可以这样写:

```ruby
['jim', 'li_lei', 'han_mei_mei'].each do |name|
  define_method "say_hi_to_#{name}" do
    puts "hi, #{name}"
  end
end

```

然后就可以调用了:

```ruby
say_hi_to_jim  # => 'hi, jim'
```

每个语言都多少有些动态改变代码逻辑的能力, 但是Ruby的元编程能力是最强的. 用起来也是最得心应手的.

ruby 元编程可以让我们做到其他传统语言无法做到的事. 大家务必知道他的重要性.

可以先不学, 但是要知道它的作用.

Rails框架之所以能这么强大, 全靠ruby的元编程能力.
