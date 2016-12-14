###使用 RSpec 进行单元测试

1. 添加下面代码到Gemfile:

```
# Gemfile:
gem 'rspec-rails', '~> 3.0.0'
```

然后运行：
```
$ bundle exec rails generate rspec:install
```
记得要把生成的.rspec 文件做个修改，删掉
# .rspec file:

```
--color
# 不要有 : --warning, --require spec_helper
```

2. 下面是测试lib文件的一个例子： make sure your have this:

```
# config/application.rb
config.autoload_paths += %W(#{config.root}/lib)
```

```
require 'rails_helper' # 这句话极度重要.
require 'html_parser'  # it's not require 'lib/html_parser'
describe HtmlParser do

  it 'should parse html_content' do
    puts 'hihihi'
  end

end
```
