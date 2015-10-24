#Bootstrap & Rails
Bootstrap是一个非常棒的CSS/HTML的框架，最初来自Twitter。它能让我们很快开发出很多web应用，并且适配各种屏幕和项目。

关于Bootstrap的介绍就不多讲了，毕竟它现在是最流行的HTML,CSS,JS框架。当我们把它和Rails一起来用的时候，会给我们带来更多的便利，我们就来看看如何把bootstarp与rails结合使用。

##下载
首先下载可以去bootstrap官网下载，官网提供三种下载：一是编译并压缩后的 CSS、JavaScript 和字体文件。不包含文档和源码文件；二是Less、JavaScript 和 字体文件的源码，并且带有文档。需要 Less 编译器和一些设置工作。三是 Bootstrap 从 Less 到 Sass 的源码移植项目，用于快速地在 Rails、Compass 或 只针对 Sass 的项目中引入。

我们可以选择任一种，只是第三种对rails进行了特定设置，使用的时候会相对更方便些。下面我们就来看看第三种的用法。

##用法
在Rails中`bootstrap-sass`是一个gem，使用起来非常简单。

在Gemfile中添加这个gem，于此同时还需要添加另一个gem`sass-rails`，来保障依赖关系。

```ruby
gem 'bootstrap-sass', '~> 3.3.5'
gem 'sass-rails', '>= 3.2'
```

然后再`bundle install`，并且重启你的rails server来让它生效。

导入Bootstrap Styles到你的项目中，打开你的`app/assets/stylesheets/application.scss`，并添加如下的代码来引用它：
```scss
// "bootstrap-sprockets"的导入必须写在 "bootstrap" 和"bootstrap/variables"之前
@import "bootstrap-sprockets";
@import "bootstrap";
```
一定要保证你写入的这个文件的扩展名是`.scss`（或者是'.sass'）。如果你是刚新建的一个rails项目，可能是会是`.css`文件扩展名，这就需要你把它改掉。

可以使用如下命令：
```console
$ mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss
```

删掉这个文件中所有的`//require`和`//=require_tree`标签，都改为`@import`来引用刚刚下载的zip包里的sass文件。不使用`//require`是因为这样就无法访问bootstrap的混合类型和变量。

在`app/assets/javascripts/application.js`中添加对bootstrap js的引用：

```js
//= require jquery
//= require bootstrap-sprockets
```

然后就可以开始在代码中使用bootstrap了。
