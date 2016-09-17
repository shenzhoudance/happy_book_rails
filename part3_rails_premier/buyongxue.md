# 新手入门不用学的东西

##  asset pipe line.

会在部署的时候给你带来大麻烦

in `config/application.rb`:


```
config.assets.enabled = false
```

## permit

in `config/application.rb`
```
config.action_controller.permit_all_parameters = true
```


# 压根不用学的rails知识

我发现下面的知识会对新手造成学习阻碍。所以，千万不要用它。

另外，学习rails之前，务必熟悉HTML的表单。务必知道request的各种参数是如何来的。否则你会被 form_for, f.input弄晕。

## scaffold

新手入门可以。但是入门时，不要依赖它。

仅仅把它用在学习的过程中。例如，我希望知道，一个完整的 CRUD， 是需要哪些文件来
配合的。那么一个最快速的办法就是使用 `$ rails g scaffold users  name:string ..`
这个命令来生成。就能看到：会生成controller, model, view, helper ... 所有依赖的文件。

也会生成 migration 等等。

根据我的经验， 依赖这个命令（以及类似命令 rails g model, rails g controller）的新人，
入门的速度，可能要达到3个月。但是，不依赖这个命令，所有的CRUD，都自己手写的人，
2~3周入门。

## form validation

在服务器端，验证一个 form object , 是否符合要求。例如， name  是否是空。 例如： email
是否是 xx@yy.com 这个格式。

我用了7年rails, 到现在，我还一直不用这个功能。对于对象的验证，在前端做（PC WEB端）做
就可以了。因为我经历的系统，对安全性都不是特别特别的看重（如果你的负责的系统，不是BAT
这样类型的公司的，也不会直接暴露给外部的用户去用，你的用户都是某个公司内部的职员的话），
就不要用这个 form_validation. 会特别麻烦。

解决办法：直接用jquery validate, 在client端进行验证。


## 关联关系。

忘掉 has_many_and_belongs_to .

不用这个，我们要用： has_many 来代替。
多对多的关系，不是由两个对象（表）来决定的。是由 3个表来决定的。例如：

老师-学生 = N:N

1. 有个老师表
2. 有个学生表
3. 有个中间表（课程，成绩）

所以，忘记 hmabt 。

## 尽量不用单表继承。

有3个对象。

1. 男人。  2. 女人   3. 生物。

所以你也可以这样设计：

只有一个表： 就叫：  creatures

creatures
-------
id: integer
name: string
type: string

表的内容，就是；

```
id  name      type
-------------------------
1   '张三丰'  Man
2   '武则天'  Women
3   '青蛙'    Creature
```

对应的class就是：

```
class Man < Creature
end

class Woman < Creature
end

class Creature < ActiveRecord::Base
end
```

这就是单表继承。 但是不要用。 因为，Rails中，充满了太多的 “COC”， 而这些惯例，就会被
单表继承所破坏。

例如：  form_for @man ,

rails 就会以为，你传递进来的对象的class, 是 Man, 而不是 Creature. 那么，是要提交到哪个？

- /creatures/2/update
- /men/2/update

另外，一旦rails用多了，你就会发现，尽量不要改变form_for, form_tag  这样的功能。

## i18n

internationalization  ,  国际化。 忘掉它。 咱们国内项目永远用不到。而且国际化将来会
为你带来巨大的麻烦。

## Plugins

忘掉它。 我干了7年，从来没写过。在我使用ruby的第一年，好像官方就把它抛弃了。(deprecated)



## 不要使用 nested form.

例如：  我在新建 一个文章的时候，就在页面上，增加针对文章的form.
不要：  在这个form中，又新增文章，又新增 该文章的评论。 (因为这个评论是属于这个文章的）



## 不要使用：respond_to 或者 类似的方法。要么返回html, 要么返回json

下面的代码，同时支持两种格式： json, html , 实际上这是鸡肋。
```
   def destroy
     @book.destroy
     respond_to do |format|
       format.html { redirect_to books_url, notice: 'Book was successfully destroyed.' }
       format.json { head :no_content }
     end
   end
```

## nested routes,  嵌套的路由。

正常的路由：  /comments/2  表示id = 2 的 评论
奇怪的路由：  /posts/50/comments/2    表示 id = 50的文章的评论中， id = 2 的评论。

问题来了：  如果我想找出，辽宁省阜新市海州区第三小学，该怎么找？

奇怪的路由：  /provinces/4/cities/3/schools/2
我干嘛不用：  /schools/2  呢？

如果一定要带上前面的参数的话，干嘛不直接；

/provinces/4/cities/3/schools/2

/schools/2?province_id=4&city_id=3

后者我可以加上100个参数，不超过64k的长度（或者2K长度，在IE下）就可以。
如果用 nested routes, 我就没法做了

极端例子： /provinces/4/cities/3/schools/2/grade/5/class/3/student/2

但是，rails guide中，第一个就是这个例子。所以，忘掉它。


## routes中，只使用 resources,  不要使用其他的路由

下面这两种都不要用。
```
  get 'welcome/index'
  match '....'
```

routes.rb中，只应该出现： resources, root 两种路由。

## params().require().permit

除非你对 form_object 很熟悉了。 否则很多新手会蒙。

直接使用params[:users]


## 不要使用turbo links.

这个技术，是为了让客户端减少对服务器端的请求。 但是效果不理想，很容易引起js的错误。
那么我们在需要的场合，直接使用更加纯粹的one-page-app. ( vuejs, react, angular,  ... )
