#Bootstrap & Rails
Bootstrap是一个非常棒的CSS/HTML的框架，最初来自Twitter。它能让我们很快开发出很多web应用，并且适配各种屏幕和项目。

关于Bootstrap的介绍就不多讲了，毕竟它现在是最流行的HTML,CSS,JS框架。当我们把它和Rails一起来用的时候，会给我们带来更多的便利，我们就来看看如何把bootstarp与rails结合使用。

##下载
首先下载可以去bootstrap官网下载，官网提供三种下载：一是编译并压缩后的 CSS、JavaScript 和字体文件。不包含文档和源码文件；二是Less、JavaScript 和 字体文件的源码，并且带有文档。需要 Less 编译器和一些设置工作。三是 Bootstrap 从 Less 到 Sass 的源码移植项目，用于快速地在 Rails、Compass 或 只针对 Sass 的项目中引入。

我们可以选择任一种，只是第三种对rails进行了特定设置，使用的时候会相对更方便些。下面我们就来看看第三种的用法。

##用法
在Rails中`bootstrap-sass`是一个gem，使用起来非常简单。

Gem:

gem 'twitter-bootstrap-rails', '3.2.0'

$ bundle install

$ rails generate bootstrap:install static

修改application.js:

+//= require twitter/bootstrap

修改 application.css:

+ *= require bootstrap_and_overrides.css

## 去掉  turbo-links

-  <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track' => true %>
-  <%= javascript_include_tag 'application', 'data-turbolinks-track' => true %>
+  <%= stylesheet_link_tag    'application', media: 'all' %>
+  <%= javascript_include_tag 'application' %>


然后就可以开始在代码中使用bootstrap了。
