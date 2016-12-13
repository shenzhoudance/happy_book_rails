# 序列化.


## 定义

serialization  把编程语言中的数据类型, 转换成: String.

de-serialization  把某个符合格式要求的String, 转换成: 复杂的数据类型.


## Array to String:
```
irb(main):017:0* require 'yaml'
=> false
irb(main):018:0> [1,2,3].to_yaml
=> "---\n- 1\n- 2\n- 3\n"


---
- 1
- 2
- 3
```

## hash:


```
irb(main):019:0> {:a => 1, :b => 2}.to_yaml
=> "---\n:a: 1\n:b: 2\n"

---
:a: 1
:b: 2

```

## 再复杂一些:

```
{
    name: 'jim',
    sex: 'male',
    hobbies: [
        '游泳',
        '游戏',
        '美食'
    ]
}


---
:name: jim
:sex: male
:hobbies:
- 游泳
- 游戏
- 美食
```



# 反序列化


```
irb(main):081:0* YAML.load "---\n:name: jim\n:sex: male\n:hobbies:\n- 游泳\n- 游戏\n- 美食\n"
=> {:name=>"jim", :sex=>"male", :hobbies=>["游泳", "游戏", "美食"]}
```


require 'yaml'

class Apple
  attr_accessor :name
  attr_accessor :color
  attr_accessor :size
end


apple = Apple.new

apple.name = '黄元帅'
apple.color = '黄'
apple.size = 'big'

puts "== apple is: #{apple.inspect}"
puts apple.to_yaml
puts Apple.to_yaml


#== apple is: #<Apple:0x0055cc9b555f38 @name="黄元帅", @color="黄", @size="big">

#--- !ruby/object:Apple
#name: 黄元帅
#color: 黄
#size: big
#--- !ruby/class 'Apple'
