#[delayed_job](https://github.com/collectiveidea/delayed_job)
delayed_job用来处理rails中的异步延时任务,支持发送大量实时通讯、改变图片尺寸、http下载等等。

例如， 我要在controller中，发送10万封邮件。 这个操作要耗时3个小时。我们不能这样写：

```
def send_emails
  (1..1000000).each do |n|
    ## 开始处理发送邮件。
  end
  redirect_to ...
end
```

这样写的话，是不行的。 每次这个action都会timeout。

对于这个情况，我们就要让程序在后台运行。

##安装
在Active Record中使用，直接在`Gemfile`中加入
```bash
gem 'delayed_job_active_record'
```
运行`bundle install`安装后端和delayed_job gem。
Active Record后端需要一个任务表，创建命令：
```bash
$ rails generate delayed_job:active_record
$ rake db:migrate
```
就会生成一个migration:

```

class CreateDelayedJobs < ActiveRecord::Migration
  def self.up
    create_table :delayed_jobs, force: true do |table|
      table.integer :priority, default: 0, null: false # Allows some jobs to jump to the front of the queue
      table.integer :attempts, default: 0, null: false # Provides for retries, but still fail eventually.
      table.text :handler,                 null: false # YAML-encoded string of the object that will do work
      table.text :last_error                           # reason for last failure (See Note below)
      table.datetime :run_at                           # When to run. Could be Time.zone.now for immediately, or sometime in the future.
      table.datetime :locked_at                        # Set when a client is working on this object
      table.datetime :failed_at                        # Set when all retries have failed (actually, by default, the record is deleted instead)
      table.string :locked_by                          # Who is working on this object (if locked)
      table.string :queue                              # The name of the queue this job is in
      table.timestamps null: true
    end

    add_index :delayed_jobs, [:priority, :run_at], name: "delayed_jobs_priority"
  end

  def self.down
    drop_table :delayed_jobs
  end
end

```

如果是`rails 4.2`，则需要在`config/application.rb`中配置`queue_adapter`
```ruby
# config/application.rb
module YourApp
  class Application < Rails::Application
    # Be sure to have the adapter's gem in your Gemfile and follow
    # the adapter's specific installation and deployment instructions.
    config.active_job.queue_adapter = :delayed_job
  end
end
```

>如果在`Rails 4.x`中使用了`protected_attributes` gem，必须将`gem protected_attributes`放在delayed_job前面，不然会报以下错误:

>ActiveRecord::StatementInvalid: PG::NotNullViolation: ERROR:  null value in column "handler" violates not-null constraint

##开发模式(development)
在development环境下，如果rails的版本大于3.1，每当执行完100个任务或者任务完成时，程序代码会自动加载，所以在development环境更改代码后不用重启任务。


##任务队列
在任何对象中调用`.delay.method(params)`，它将在后台进行处理
```ruby
#不使用延时任务
@user.activate!(@device)

#使用延时任务
@user.delay.activate!(@device)
```

使用了 `delay`之后， 所有的任务，都要：

1. 写入到特定的“任务表”中。
2. 我们要运行一些“工人（worker）“， 来执行这些任务。

```
class User < ActiveRecord::Base
  def eat
    Rails.logger.info "== begin eating ..."
    sleep 2
    Rails.logger.info "== finished ..."
  end
end
```

```
rails console:
> User.first.delay.eat
```

```
$ bundle exec script/delayed_job -n 2 start
```
 表示，同时有两个worker 在干活儿。这两个worker, 会紧盯着“任务表”， 一有任务，马上开始执行。

```
kaikai:graduate$ bundle exec ruby bin/delayed_job -n 2 start
delayed_job.0: process with pid 31047 started.
delayed_job.1: process with pid 31053 started.
```

3. 看日志。就会发现， 这些worker 在不间断的工作。

```
# 表示 worker 开始认领了这个任务，开始执行了。
# 第一步。 认领该任务。并且，把该任务的状态，变成: locked.
17:44:52 DEBUG:   Delayed::Backend::ActiveRecord::Job Load (0.5ms)  SELECT  `delayed_jobs`.* FROM `delayed_jobs` WHERE `delayed_jobs`.`locked_by` = 'delayed_job.1 host:kaikai pid:31053' AND `delayed_jobs`.`locked_at` = '2016-11-03 09:44:52' AND `delayed_jobs`.`failed_at` IS NULL  ORDER BY `delayed_jobs`.`id` ASC LIMIT 1
# 第二步。 开始执行了。
17:44:52 INFO: == begin eating ...
17:44:52 DEBUG:   User Load (0.3ms)  SELECT  `users`.* FROM `users` WHERE `users`.`id` = 1 LIMIT 1
# 这个是delayed job的日志
17:44:52 INFO: 2016-11-03T17:44:52+0800: [Worker(delayed_job.1 host:kaikai pid:31053)] Job User#eat (id=2) RUNNING
17:44:54 INFO: == finished ...
# 第三步。 执行完毕，从delay_jobs （任务表）中，删掉干才干完的任务。
17:44:54 INFO: 2016-11-03T17:44:54+0800: [Worker(delayed_job.0 host:kaikai pid:31047)] Job User#eat (id=1) COMPLETED after 2.2580
# 因为它干完一个任务就删掉一个， 所以，当delay_jobs 表中，没有任何记录的时候，就是活儿干完
的时候。
```




如果一个方法需要一直在后台运行，可以在方法声明之后，调用`handle_asynchronously`
```ruby
class Device
  def deliver
    #长时间运行方法
  end
  handle_asynchronously :deliver
end

device = Device.new
device.deliver
```
###参数
`handle_asynchronously`和`delay`有下列参数：
* :priority (number):数值越小越先执行，默认为0，但可以重新配置
* :run_at (time):在这个时间后执行任务
* :queue(string):任务队列名，是priority的另一种选择

```ruby
class LongTasks
  def send_mailer
    # Some other code
  end
  handle_asynchronously :send_mailer, :priority => 20

  def in_the_future
    # Some other code
  end
  #五分钟后将执行(5.minutes.from_now)
  handle_asynchronously :in_the_future, :run_at => Proc.new { 5.minutes.from_now }

  def self.when_to_run
    2.hours.from_now
  end

  class << self
    def call_a_class_method
      # Some other code
    end
    handle_asynchronously :call_a_class_method, :run_at => Proc.new { when_to_run }
  end

  attr_reader :how_important

  def call_an_instance_method
    # Some other code
  end
  handle_asynchronously :call_an_instance_method, :priority => Proc.new {|i| i.how_important }
end
```
如果在调试的时候要调用一个`handle_asynchronously`的方法，但是不想延迟执行，这时候可以在方法名后面加上`_without_delay`，例如原方法名叫做`foo`，立即执行为`foo_without_delay`

##运行任务
`script/delayed_job`能够用来管理后来进程，开始任务。
添加`gem "daemons"到`Gemfile`，并且执行`rails generate delayed_job`
```bash
RAILS_ENV=production script/delayed_job start
RAILS_ENV=production script/delayed_job stop

# 在不同的进程上执行两个worker
RAILS_ENV=production script/delayed_job -n 2 start
RAILS_ENV=production script/delayed_job stop

# 使用--queue或者--queues选项来指定不同的队列
RAILS_ENV=production script/delayed_job --queue=tracking start
RAILS_ENV=production script/delayed_job --queues=mailers,tasks start

#使用--pool选项来指定worker pool，可以多次使用这个选项来给不同队列启动不同数量的worker

#下面启动一个worker执行`tracking`队列，两个worker执行`mailers`和`task`队列，另外两个worker给其他的任务
RAILS_ENV=production script/delayed_job --pool=tracking --pool=mailers,tasks:2 --pool=*:2 start

# 执行所有任务，然后退出
RAILS_ENV=production script/delayed_job start --exit-on-complete
#或者直接在前端执行
RAILS_ENV=production script/delayed_job run --exit-on-complete
```
>Rials 4 需要替换`script/delayed_job`为`bin/delayed_job`
worker可以运行在任何电脑上，只要它们能够访问数据库并且时钟保持同步。每个worker至少每5秒钟检查一次数据库。

可以使用`rake jobs:work`来开启任务，使用`CTRL-C`终止rake任务。
如果需要执行所有任务并退出，可以使用`rake jobs:workoff`
处理队列通过设置环境变量`QUEUE`或者`QUEUES`
```bash
$ QUEUE=tracking rake jobs:work
$ QUEUES=mailers,tasks rake jobs:work
```

##重启delayed_job
重启delayed_job：
```bash
$ RAILS_ENV=production script/delayed_job restart
```
重启多个delayed_job workers：
```bash
$ RAILS_ENV=production script/delayed_job -n2 restart
```
>Rials 4 需要替换`script/delayed_job`为`bin/delayed_job`

###清除任务
使用`rake jobs:clear`删除队列中的所有任务
