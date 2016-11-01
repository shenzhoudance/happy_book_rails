# Rails 的学习过程

市面上的rails入门, 书特别厚. 像块砖头一样.

其实还可以写的更厚.

我在rails 2.x 的年代把rails guide打印出来过. 400多页A4纸.

让新手望而却步.

## 可以很简单

确定一些内容是不学的.(见上面)

剩下的内容, 是要有学习过程的.

这里我要吐槽rails的官方文档. 它的第一张 rails tutorial, 完全不是入门. 而是一种 "技能的炫耀".

这个教程, 我让30个小白纸(都是应届毕业生) 来学, 学习2周, 没有一个能看懂. 就是因为它是
rails的特性的炫耀, 上来就给出一堆花里胡哨的概念和技巧, 充满了各种缩写, 各种概念和各种晦涩的东西.

当年我做了3年半的java web开发, 买了两本书(ruby 锄头书 和 rails滑板书), 结果还是无法快速入门rails.

后来创业后, 我招不到人, 就只能自己培养, 于是发现对于新手, 教学的时候务必要一点一点的教,
前面一个概念弄明白了, 我再讲后面的概念. 绝对不能把 router, controller, 表单对象和model的东西
放在一堂课中讲.

## 正确的学习路径

对于小白纸, 正确的入门路径如下:

1. 学习好 HTML + css

这个很重要. 学习好了html的标签,就知道后面的 form_for, select_tag, link_to 都是什么了.

2. 学习好ruby语言.

只学习最常见的20%功能即可. 能知道最最基本的语法, symbol是什么, 常见的简写方式,
理解block的概念, 知道"@"变量 就可以了.

3. 学习好数据库.

只学习:
3.1 select, insert, update, delete 这些基本语句
3.2 where 查询语句
3.3 join 语句, 知道什么是关联和外键即可.

4. rails第一课

学习 如何安装rails, 知道bundler是什么, 写个hello world并显示出来. (务必不要学习增删改查)

5. rails第二课. router

学习 路由. 只学习最常用的restful路由即可. 这里是rails 最最复杂的三大概念之一. 路由的作用跟
脊柱一样, 把M-V-C 这三大块都串联了一起. 所以务必单独拿出来学习.

而新手往往苦于对 路由不熟悉, 结果后面的controller, form object都没办法学.

6. rails第三课  view

视图相对来说简单, 需要学习的前提知识是 router 和 html. 所以放在router之后讲. 侧重于学习它的
展现方面的知识. (在其他语言中, PHP,JSP 有这一个视图就可以入门了,所以国内学PHP的人总认为它好学)

7. rails第四课 命令行, database migration 和 model 层.

7.1 先讲 rails的命令行, 因为只有在命令行下面才能使用 migration

7.2 再讲数据库额的配置和迁移. migration 是rails的自带王牌. 学习的前提知识是 数据库的知识.
要熟悉对数据库的基本操作和关联关系. 所以放在这里. 我们这一课只讲 migration.

7.3 再讲model.  务必记得model不要跟 form object 和 controller 一起学习, 新手一定会蒙圈.
所有model的操作(增删改查)都要放在 rails console中讲解. 这样才能不跟view, controller混在一起
切记切记.

这些内容关联的特别紧密.

8. rails第五课. Controller 和 form object.

到现在,我们终于可以学习controller 的知识了. 有了前面router, view的铺垫, 同学们对于controller
上手就会特别容易.

重点是 form object, 这里是rails三大难之二. 没有表单对象这个web实现模式的同学, 都是难以理解
rails中的各种form_for, form_tag, form_helpers的.  所以,这里我们要向大家展开一扇新的大门.
学会了form object, 那么 java 世界的第一个框架 struts 就基本学到手了.

然后是 form helpers. 它是把 <input>, <select> 等表单标签使用rails的形式来表示的组件. 这里的
前提知识是 html form 标签. 知道了这个, 就知道如何使用了.

最后,就是如何在controller中读取request中传递过来的参数(往往是 form object的形式).

(其实学习到了这里, rails 的最常见20% 知识就学习完了.)

9. rails第六课. layout, asset pipeline 与 部署.

layout: 布局. 它的学习前提知识是 : view, controller. 所以在这里学习. layout也是一种关键的实现模式,
简单,有效.

asset pipeline , 是rails三大难之三. 哪怕是老手, 我都遇到过完全搞不定这个东东的朋友. 这里的

我觉得它的难在于:
- 前提知识是: layout, view helper 和 tcp协议的优化知识.
- 部署时会遇到 linux 的权限知识
- 调试时会遇到 nginx 的调试知识

所以, 大家看到没有, 只要用到的知识点一多, 这个环节就不好学.

最后,是 rails的部署. 这里不难.几个命令搞定. 或者按照给定的nginx配置去做就可以了.

10. rails 第七课. 常用的组件

这些都是我统计了自己所有的web项目后,总结出来的跟 web后端相关的组件.

都是轮子,我们按照官方网站的介绍拿过来用就可以.

10.1  分页(Kaminari)，上传图片(carrier wave)，上传到upyun，发送短信， 所见即所得编辑器，
10.2  发送http 请求（httparty）, 日志工具(log4r)， 全局配置工具(rails-config), HTML 分析工具（nokogiri）, migration注释，
10.3  bootstrap-rails, 执行定时任务（rufu-scheduler）, 执行后台任务(god),  执行延时任务（delayed_job）, 使用capistrano 进行自动化部署。


