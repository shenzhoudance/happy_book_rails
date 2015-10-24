#[Rake](http://guides.ruby-china.org/command_line.html)
> 参考文档: http://guides.rubyonrails.org/asset_pipeline.html

Rake是与Make类似的构建语言,用来编写任务脚本,有独特的DSL语言来编写Ruby中的各种命令以及任务,根据Rakefile和.rake来构建任务。

在Rails中使用Rake来管理任务

- ` rake -- tasks`列出所有可用的任务;
- `rake db:create --trace`查看某个任务调用的完整堆栈;
- ` rake -T `查看所有的任务;
- `rake about` 任务输出以下信息：Ruby、RubyGems、Rails 的版本号，Rails 使用的组件，程序所在的文件夹，Rails 当前所处的环境名，程序使用的数据库适配器，数据库模式版本号;
 
##assets

- `rake assets:precompile` 任务会预编译 app/assets 文件夹中的静态资源文件;
- `rake assets:clean` 任务会把编译好的静态资源文件删除;

##db
- `rake db:create` `rake db:migrate` 数据库创建和迁移的两个命令;
- `rake db:version` 输出数据库的当前版本;

> `db`是命名空间,更多命令请看[Active Record Migrations](http://guides.rubyonrails.org/active_record_migrations.html)章节;

##doc
- `rake doc:app` 在 doc/app 文件夹中生成程序的文档;
- `rake doc:guides` 在 doc/guides 文件夹中生成 Rails 指南;
- `rake doc:rails` 在 doc/api 文件夹中生成 Rails API 文档;

##notes
rake notes 会搜索整个程序，寻找以 FIXME、OPTIMIZE 或 TODO 开头的注释

- `rake notes:todo` 搜素整片文章中的TODO;

##routes

- `rake routes` 查看所有的路由;

##test

- `rake test` 运行所有单元测试，功能测试和集成测试;

> Rake默认任务是测试。`test`是命名空间,更多命令请看[Testing Rails Applications](http://guides.rubyonrails.org/testing.html)章节;

##tmp

tmp: 命名空间中的任务可以清理或创建 Rails.root/tmp 文件夹:

- rake tmp:cache:clear 清理 tmp/cache 文件夹;
- rake tmp:sessions:clear 清理 tmp/sessions 文件夹;
- rake tmp:sockets:clear 清理 tmp/sockets 文件夹;
- rake tmp:clear 清理以上三个文件夹;
- rake tmp:create 创建会话、缓存、套接字和 PID 所需的临时文件夹;

##其他任务

- rake stats 用来统计代码状况，显示千行代码数和测试比例等;
- rake secret 会生成一个伪随机字符串，作为会话的密钥;
- rake time:zones:all 列出 Rails 能理解的所有时区;

##Rake 任务

自定义的`Rake`任务保存在 Rails.root/lib/tasks 文件夹中，文件的扩展名是 `.rake`。执行 `bin/rails generate task` 命令会生成一个新的自定义任务文件。
