#[rufus-scheduler](https://github.com/jmettraux/rufus-scheduler)

##简介

能够定时的执行命令的`rails gem`

##安装

在rails项目中的`Gemfile`中添加`gem 'rufus-scheduler'`后`bundle install`即可

##使用

###准备脚本

- 在项目目录下新增scripts文件夹
- 增加`test_rufus_scheduler.rb	`文件
- 写入如下代码

```ruby
# -*- encoding : utf-8 -*-
ENV['RAILS_ENV'] = ARGV.first || ENV['RAILS_ENV'] || 'production'
require File.expand_path(File.dirname(__FILE__) + "/../config/environment")
require 'rubygems'
require 'rufus/scheduler'
require 'rails'
require 'uri'
scheduler = Rufus::Scheduler.new

scheduler.every '180s' do
  # 每隔180s执行
end

scheduler.in '5s' do
  # 5s时执行
end
  
scheduler.in '10d' do
  # 10天时执行
end

scheduler.at '2030/12/12 23:30:00' do
  # 在某一个详细的时间段执行
end

scheduler.every '3h' do
  # 每隔3h执行
end

scheduler.cron '5 0 * * *' do
  # 能用crontab(http://crontab.org/)的表达形式
  # 每天凌晨5分钟后执行
end

scheduler.join
```

###启动脚本

- `bundle exec ruby scripts/test_rufus_scheduler.rb`

- 如果需要以后台运行形式启动可加上`nohup`,如:`nohup bundle exec ruby scripts/test_rufus_scheduler.rb`
> 查看进程可通过`ps ef | grep ruby`查看