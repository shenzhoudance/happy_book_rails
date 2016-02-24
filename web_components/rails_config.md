# 使用配置文件

rails 自带配置文件。比如，我们可以把配置写到：

- config/application.rb
- config/environments/developement.rb
- config/environments/production.rb
- config/environments/test.rb

也可以把配置写到：

- config/initializers/xyz.rb

但是，上面几个文件都不如 yml 文件好:

- yml 文件的配置属性更强大一些。也更加易读。前面的文件都是系统核心文件。
- rails中有很多阶段，例如：
  - 刚启动
  - 开始加载 config/application.rb
  - 加载完毕 config/application.rb
  - 开始加载 config/environments
  - 加载完毕 config/environments 中各种文件等
  希望在任意一个阶段使用某个常量，都是需要确保这个常量是被定义好的。

  我们使用 railsconfig 可以轻易的在 rails最开始加载的时候，就配置好某个常量。

所以，我们要使用 railsconfig.

## 安装

在 Gemfile中：

```ruby
gem 'config'
```

然后 `$bundle install` 即可。

## 使用

- 生成配置文件：
```
$ bundle exec rails g config:install
```

就会生成下面若干文件：
```
# 必须的文件。
# config/initializers/config.rb

RailsConfig.setup do |config|
  # 这里指定了配置对象的名称
  config.const_name = "Settings"
end
```

以及下面的配置文件（如果你喜欢简单的话，可以只保留 `config/settings.yml`）
```
# 全局的配置文件。
config/settings.yml
# 开发模式下的文件。
config/settings/development.yml
# 生产模式下的配置文件。
config/settings/production.yml
# 测试模式下的配置文件。
config/settings/test.yml
```

其中，后面三者的内容都会直接覆盖 config/settings.yml 的内容。

编辑对应的配置文件。

```yml
username: 'uubpay'
password: 'goodday!'
```

使用方法如下：

```rb
login_upyun(
  username: Settings.username,
  password: Settings.password
)
```
