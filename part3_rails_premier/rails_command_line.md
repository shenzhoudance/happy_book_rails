# Rails命令行

本部分讲解Rails开发过程中一些常用的命令操作,适合于具有Rails入门基础的读者.
阅读完本部分你将掌握创建Rails程序,生成模型、控制器、数据库迁移,单元测试以及开启测试服务器和对程序进行调试等基础知识。
未安装Rails的读者可以通过执行
```
$ gem install rails
```
命令安装Rails.

## 基本Rails命令

Rails开发过程中可以在命令后添加 -h或--help 用于显示命令的帮助信息.
例如:
```
$ rails server --help
```

### 创建Rails程序

初始化一个完整Rails可执行项目:
```
$ rails new appname
```
使用指定的Rails版本生成项目:
```
$ rails _4.1.6_ new myapp
```
rails new命令会默认生成基于Sqlite数据库的项目,生成指定数据库项目可执行如下命令:
```
$ rails new mysqlp -d=mysql
```

### 启动项目rails server(简写:rails s)

Rails开发过程中可以对代码实时的改进,在项目目录下执行此条命令用于启动Ruby内建的小型服务器,每次修改代码后无需重新执行此条命令,
保存文件刷新浏览器页面即可看到改动.
常用rails server命令:
```
$ rails s                 # development模式启动
$ rails s -e production   # production模式启动
$ rails s -p 3333         # 以3333端口启动
$ rails s -u              # 用于debugger调试
```
执行以上命令后打开浏览器,访问http://localhost:3000,即可看到生成的Rails程序.
(在常规的Rails程序中，URL的格式是http://(host)/(controller)/(action)，访问 http://(host)/(controller) 会进入控制器的 index 动作。)

### rails generate(简写:rails g)

rails generate命令用于使用模板生成很多东西。单独执行 rails generate 命令，会列出可用的生成器.
```
$ rails g                                                     #查看有哪些生成器
$ rails g controller mycontroller index --no-test-framework   #创建一个控制器
$ rails g model mymodel                                       #创建一个model
$ rails g scaffold HighScore game:string score:integer        #使用脚手架建一个名为“HighScore”的资源，记录视频游戏的最高得分
$ rails g migration add_column_to_table                       #添加自定义的迁移文件
```

### rails console(简写:rails c)

执行 console 命令后，可以在命令行中和 Rails 程序交互。在终端里可以快速测试想法，或者修改服务器端的数据，而无需在网站中操作。
常用命令如下:
```
$ rails c                        #development模式
$ rails c --sandbox              #沙箱模式 不会修改数据,用于测试代码,读取或修改数据以及读取配置
$ rails c production             #production模式
```
### rails dbconsole(简写:rails db)

rails dbconsole不仅能检测到你正在使用的数据库类型,还能理解传入的命令行参数，然后进入该数据库的命令行界面。
该命令支持 MySQL，PostgreSQL，SQLite 和 SQLite3数据库。

### rails runner(简写:rails r)

runner 可以以非交互的方式在 Rails 中运行 Ruby 代码
```
$ rails r 'Model.long_running_method'               # 执行程序
```

### rails destroy(简写:rails d)

rails destroy可以理解为rails generate的逆操作,如下所示:
```
$ rails g model mymodel           #用于创建model
$ rails d model mymodel           #用于删除model
```

## Rake命令

### 基础rake命令

```
$ rake --tasks(或rake -T)             #查看所有的rake命令
$ rake -P                             #查看所有rake，不展示描述
$ rake about                          #查看环境相关信息,包括Ruby、RubyGems、Rails 的版本号，Rails 使用的组件，
                                      #程序所在的文件夹，Rails当前所处的环境名，程序使用的数据库适配器，数据库模式版本号
$ rake -W                             #查看rake源码位置，调试很有帮助
$ rake assets:precompile              #预编译app/assets文件夹中的静态资源文件
$ rake assets:clean                   #删除已编译的静态资源
$ rake routes                         #查看现有路由
$ rake secret                         #生成一个用于session secret的随机值
$ rake middleware                     #查看当前环境使用了哪些中间件
$ rake stats                          #生成代码统计
$ rake doc:app                        #在doc/app文件夹中生成程序的文档
$ rake doc:guides                     #在doc/guides文件夹中生成 Rails 指南
$ rake doc:rails                      #在doc/api文件夹中生成 Rails API 文档
$ rake notes                          #会搜索整个程序，寻找以 FIXME、OPTIMIZE 或 TODO 开头的注释
                                      #搜索的文件包括 .builder，.rb，.erb，.haml，.slim，.css，.scss，.js，.coffee，.rake，.sass 和 .less
                                      #搜索的内容包括默认注解和自定义注解
$ rake notes:xxx                      #查找特定的注解,在命令中注解的名字要使用小写形式
```

### tmp:命名空间下的rake命令

```
$ rake tmp:cache:clear                #清理 tmp/cache 文件夹
$ rake tmp:sessions:clear             #清理 tmp/sessions 文件夹
$ rake tmp:sockets:clear              #清理 tmp/sockets 文件夹
$ rake tmp:clear                      #清理以上三个文件夹
$ rake tmp:create                     #创建会话、缓存、套接字和 PID 所需的临时文件夹
$ rake time:zones:all                 #列出 Rails 能理解的所有时区
```

### db:命名空间下的命令

```
$ rake db:create                                #依据DATABASE_URL 或者 config/database.yml 中Rails.env 环境创建database(development 和test)
$ rake db:create:all                            #将创建配置中所有database
$ rake db:drop                                  #依据配置删除数据库(development test)
$ rake db:drop:all                              #删除配置中所有数据库
$ rake db:reset                                 #先drop掉数据库，然后依据schema.rb文件重新生成数据库和表
$ rake db:migrate                               #迁移当前环境的数据库(会调用 db:schema:dump 以更新db/schema.rb)
$ rake db:migrate VERSION=20080906120000        #使当前数据库迁移到指定的版本号，首先判断指定版本号和当前数据库版本号的顺序，然后视情况执行up(change)或者down
$ rake db:migrate:up VERSION=20080906120000     #对指定版本号的migrate执行up操作
$ rake db:migrate:down VERSION=20080906120000i  #对指定版本号的migrate执行down操作
$ rake db:migrate:status                        #查看数据库迁移状态
$ rake db:version                               #查看当前migrate版本号
$ rake db:rollback [STEP=n]                     #对最近的n次migrate执行down操作，不传递STEP的话n是1
$ rake db:seed                                  #加载 db/seeds.rb 文件
$ rake db:setup                                 #基本上等于 db:reset + db:create + db:schema:load + db:seed
```

## 测试相关

```
$ rake db:schema:dump          #基于当前环境数据库, 以ruby的形式, 生成db/schema.rb文件, 另外每次migrate后也会自动调用该任务
$ rake db:schema:load          #加载schema.rb 以格式化当前环境数据库
$ rake db:structure:dump       #基于当前数据库, 以sql的形式, 生成db/structure.sql. 可通过参数指定其他文件: DB_STRUCTURE=db/my_structure.sql
$ rake db:test:load            #基本等同rake db:schema:load 只是会用schema.rb构建测试库
$ rake db:test:clone           #等同于 db:schema:dump 加上 db:test:load , 不会复制数据库中的数据
$ rake db:test:clone_structure #使用的是{rails_env}_structure.sql 而不是schema.rb
$ rake db:test:prepare         #检查开发模式有无pending migrate, 如果有，则停止并且提示，如果没有，则 test:clone_structure 或者 test:load
```
