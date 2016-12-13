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






## 设计模式

30年以前, 那时候的主流语言: c, vb, 汇编.

操作非常底层, 非常原始. 甚至一些语言, 没有循环的语法.
只有 go-to .  通过go-to ,来实现循环的目的.

例如:  我们希望对一个数组,做遍历.  现在这样写:

```
[1,2,3].each do |e|
   puts e
end
```

之前是:

```
for .. each
end
```

再往前:

```
a = [1,2,3]

for( i = 0 ; i < a.length; i++){

}
```

再往前:

```
a = [1,2,3]
i = 0
while(i < a.length ) {
  System.out.println("e is: " + e);
  i ++;
}

```

再往前:  (更不好理解了)

```
   int a = 10;

   /* do loop execution */
   LOOP:do {

      if( a == 15) {
         /* skip the iteration */
         a = a + 1;
         goto LOOP;
      }

      printf("value of a: %d\n", a);
      a++;

   }while( a < 20 );

```

## 痛点:

1. 大家的代码,一不小心,就写的特别乱.
2. 代码不好读.

所以,一些老手, 慢慢的,就发展出了,一些面对特定问题的固定的解决写法.
我们管这个叫"模式".  (在国外, pattern, 是一个建筑工程师, 首次提出来的.
alexandar(?) )

跟今天的 按照习俗来编程是一样的.

经典的设计模式. <<Design patterns>> , 作者4个人. Gang of Four.

设计模式,最初,只口头流传与, 业内的顶级高手. 于是他们四个就做整理.
梳理出 24种设计模式.  包括三大类.

所以, 如果不懂设计模式,就== 不同面向对象编程.

创建类
  Singleton   :
		a = DatabaseTool.getConnection()

	Prototype


  Builder
  Factory
  Factory Builder

行为类
  Iterator
	Visitor
  Listener
  Command


class EditorCommand

  def redo
	end

  def undo
  end

  def execute
	end

end



  Responsibility Chaining

结构类
  Bridge
  Strategy
  Adapter


MVC: 是一种 "思想". 层面非常高, 非常概括行的"分层思想"


form object 思想:

封装.  把一个form中零散的20个属性, 封装成一个大的对象. 这个对象,就是表单对象.


<form action='/books'>
  <input type='text' name='tit'

</form
