# 日志:   log4r

日志是我们调试的最重要的手段，没有之一。

我们可以通过debug, 断点来人肉找到问题，但是如果没有把信息保存到文件中，就无法
事后找到线索。

最好的日志应该是 每日分卷, 有时间戳，可以控制输入级别。

我第一次看到最好的日志是在2006年，在java blog roller(https://roller.apache.org/)
上。简直惊艳。

在ruby中，虽然有自带的logger, 但是跟log4r还是有很大距离的。也很麻烦。

**TODO：请在此处说明ruby自带的logger和log4r之间的优缺点比较，或直接列举出log4r的优势。**

所以，就出现了log4r ( log for ruby ).

# 使用步骤

1.新建一个log4r的配置文件：  config/log4r.yml

```yaml
log4r_config:
  # define all loggers ...
  loggers:
    - name      : production
      level     : WARN
      trace     : 'false'
      outputters :
      - datefile
    - name      : development
      level     : DEBUG
      trace     : 'true'
      outputters :
      - datefile

  # define all outputters (incl. formatters)
  outputters:
  - type: DateFileOutputter
    name: datefile
    dirname: "log"
    filename: "my_app.log" # notice the file extension is needed!
    formatter:
      date_pattern: '%H:%M:%S'
      pattern     : '%d %l: %m '
      type        : PatternFormatter
```

2.修改`config/application.rb`

```ruby
require 'rails/all'
# add these line for log4r
require 'log4r'
require 'log4r/yamlconfigurator'
require 'log4r/outputter/datefileoutputter'
include Log4r

Bundler.require(:default, Rails.env) if defined?(Bundler)
module Zurich
  class Application < Rails::Application
    #...
    # assign log4r's logger as rails' logger.
    log4r_config= YAML.load_file(File.join(File.dirname(__FILE__),"log4r.yml"))
    YamlConfigurator.decode_yaml( log4r_config['log4r_config'] )
    config.logger = Log4r::Logger[Rails.env]
  end
end
```

3.修改Gemfile, 增加log4r的配置. 注意版本不能低于 `1.1.9`.否则不支持每日分卷

```ruby
gem 'log4r', '1.1.9'
```

4.如果你用的是Rails4, 那么需要增加下面这个文件

```ruby
# config/initializers/log4r_patch_for_rails4.rb
class Log4r::Logger
  def formatter()
  end
end
```

现在就可以了。 进入到你的 Rails.root, 重启rails, 就会发现`log`目录开始分卷了。

```
May  9 17:05 rails_2011-05-09.log
May 10 13:42 rails_2011-05-10.log
```

日志的内容看起来如下：

```bash
$ tail log/rails_2011-05-10.log
Started GET "/????_settings/19/edit" for 127.0.0.1 at ...
13:42:11 INFO:   Processing by ????SettingsController ...
13:42:11 INFO:   Parameters: {"id"=>"19"}
13:42:12 DEBUG:   ????Setting Load (0.0ms)  SELECT "d ...
13:42:12 INFO: Completed 200 OK in 750ms
```

**TODO：请试着用一条日志举例，详细说明log4r的日志特色。让新同学明白该怎样学会看日志，更好的话可以谈谈自己看错误日志的经验，怎样快速有效的在众多日志中获取有用的信息。**

**TODO：本章节需要改进的地方在于，章节内容只纯粹告诉了我怎样安装log4r，并没有告诉我为什么要使用log4r，它的优势在哪儿；安装完log4r后我该怎样使用好这个工具，把它的优势展现出来。否则，装了log4r却不会如何有效的去查看错误日志等于白装。**
