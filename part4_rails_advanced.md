## self

在普通的ruby class中，self 等同于 这个class.   例如：

```ruby
class Apple
    def self.color   # 等同于：  def Apple.color
    end
end
```

在 ActiveRecord中， self 很多时候指带某个instance ,例如：

```ruby
class Apple  < ActiveRecord::Base
  before_save
    # 这里的self 指 当前正在被保存的 apple instance.
    self.name = 'new_name'
  end
end
```
