###使用 RSpec + Factory Girl 高效测试

RSpec是一种描述性语言，通过可行的例子描述系统行为，非常容易上手，测试用例非常容易理解。Factory Girl可以很好的帮助构造测试数据，免去了自己写fixture的烦恼。

第一步 安装rspec和rspec-rails

在命令行中执行如下命令：

	$ sudo gem install rspec v = 1.3.0
	$ sudo gem install rspec-rails v = 1.3.2
安装完成后，进入rails应用所在的目录，运行如下脚本，生成spec测试框架：

	$ script/generate rspec          
    	exists  lib/tasks
 	identical  lib/tasks/rspec.rake
 	identical  script/autospec
 	identical  script/spec
    	exists  spec
 	identical  spec/rcov.opts
 	identical  spec/spec.opts
 	identical  spec/spec_helper.rb
 	
第二步 安装factory-girl

在命令行中执行如下命令：

	$ sudo gem install factory-girl
	
在config/environment/test.rb中，加入factory-girl这个gem：

	config.gem "factory_girl"
	
在spec/目录下，增加一个factories.rb的文件，用于所有预先定义的model工厂。	

