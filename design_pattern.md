# 继承， interface 与mix-in

## 继承
它们都是面向对象的一些概念。

大学刚入学，都要学C/C++。 有概念，叫继承。

例如：

人 是 生物。

class 生物

end

class 人 extends 生物
end

这样的话，通过继承， 人 就具备了 生物的 所有属性，方法。

C 语言：  可以多重继承。

人 又能继承 哺乳动物， 又能继承 素食动物。

继承缺点： 继承的多了，特别容易复杂。

事实上， java / oc 是不允许多重继承的。

## interface

为了弥补 不能多重继承的缺点， 以及一些其他语法上的需要， 出现了interface

设计模式 98年左右出现， 经典的设计模式，有24种， 设计模式里面，大量的使用了interface.
因为 interface 可以被多重 实现。

例如：

interface Fruit { }
interface GreenObject{ }
interface SweetObject{ }


那么我就可以声明一个class, 同时实现这三个接口。

class 绿葡萄 implements Fruit, GreenObject, SweetObject

从面向对象的角度出发。 这样设计， 很精巧。

所以，在传统语言（java, c, ... )中， 设计模式，大行其道。

缺点：

1. 设计模式很精巧， 各种多重implementation 也是被用的特别广泛。这是一种：语法糖。
个人感觉：  我们去医院， 被打一针， 疼的嗷嗷哭。 家长给个糖吃。

编程之路一路走来各种虐心，最后，使用设计模式，感觉稍微好一点点。
总体： 还是感觉吃了大亏。

2. 设计模式，特别不直观。 每个模式，都要跟语言的语法挂钩。 如果你不了解那门语言，
就用不好设计模式。

3. 在现代语言中，特别是 支持block的语言中（ ruby, javascript, python ) 设计模式，几乎用不到。

同理， 现代语言，认为 interface 是多余的。


## mix-in

1. 定义一些高度抽象的module. （模块）
2. 一个class, 可以 包含（include) 多个module.

跟多重继承有些相似，但是有很大区别。  不会像多重继承那样，把对象给弄乱了。

module Fruit
  def can_eat?
    true
  end
end

module GreenObject
  def color
    'green'
  end
end

class Apple
  include Fruit
  include GreenObject
end


green_apple = Apple.new
green_apple.color  # => 'green'




