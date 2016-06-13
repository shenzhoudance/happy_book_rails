# Rails 入门

## 新手军规

不要用 rails g 这个命令。

除了migration, 无论是view, 还是controller,  还是什么，都不要用 rails generate  这个命令。

对于新手来说特别重要！

人肉的敲入命令，能加深大家的记忆和理解。

## 可以在任意controller中调用某个model

很多朋友都问：在 UsersController中如何调用 Book?

答： 没有任何限制。

```
class UsersController ...

   def index
     @books = Book.all
     @users = User.all
   end
end
