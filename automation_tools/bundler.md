# Bundler 

>-- The best way to manage a Ruby application's gems

bunlder就是一个rubygem依赖关系的管理工具，而它本身就是一个gem，即a gem to bundle gems。

由于RubyGems批处理运行。在配置文件Gemfile里说明你的应用依赖哪些第三方包，他自动帮你下载安装多个包，并且会下载这些包依赖的包。

##安装和使用

因为它本身也是一个gem，所以使用`gem install`来安装它：

`$ gem install bundler`	

然后初始化：

`$ bundler init`

假设我们安装一个gem，叫rspec，使用命令来在Gemfile中添加对rspec这个gem的声明，也可以手动写入Gemfile文件里：

`$ echo 'gem "rspec"' >> Gemfile `

执行安装，安装的时候，是根据Gemfile里的声明来安装的：

`$ bundle install`

查看已经安装的gem及其版本号：

`$ bundle show` 或者使用 `$ bundle list`

使用bundler来执行你安装的gem:

`$ bundle exec rspc`
	
##说明

使用bundler能帮助你管理你项目中所依赖的gem。在Gemfile中给定一个gem的列表，bundler能够自动的下载并且安装它们，也包括一些必须要的gem。但是有一点需要注意，就是在执行`bundle install`之前，要确认你写在Gemfile中的gem，有确切的版本，这样能减少一些版本冲突之类的问题。

使用命令`bundle update`， bundler也能帮你更新你的gem版本，`bundle update`会去检查Gemfile里gem的更新，然后对比Gemfile.lock文件，如果Gemfile里没有指定版本或是指定是>=的版本，那有新版本就会去安装新的版本的gem，然后更新Gemfile.lock文件，当然不建议随便bundle update，因为全部更新之后可能会产生错误，建议更新单个gem。
