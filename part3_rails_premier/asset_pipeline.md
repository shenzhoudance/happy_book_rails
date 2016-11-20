# assets pipeline

所有的 js, css, image, 都叫 assets.

## 为啥用它.

传统给页面增加 js，css的方式, 是一堆CSS罗列上去：

```
<link rel="stylesheet" href="./static/buttons.css">
<link rel="stylesheet" href="./static/normalize.css">
<link rel="stylesheet" href="./static/font-awesome/css/font-awesome.css">
<script src="./static/javascript/jquery.min.js"></script>
<script src="./static/javascript/jqueryui.min.js"></script>
<script src="./static/javascript/bootstrap.min.js"></script>
```

以上内容，会在页面打开时增加负担： 多加载N个http request( 上面是 3个css, 3个js )。

一般的项目， 往往会用到 上百个 js.css.

(例如： http://sina.com.cn). 每次请求，至少要发出500+ 个请求，

其中 js 请求 161个， CSS 2 个（这个还可以）)

再比如， 163.com  , 总共 400+ 请求， 8个 css，  121个 js 请求）

见下面的gif:

![163页面的打开过程](images/163.com-requests.gif)

所以，优化HTML页面的核心：减少 http请求。

我们的web开发中, 最耗费时间的不是请求数据库，而是 浏览器端跟 服务器 建立连接的过程。

## 传统，如何减少js/ css 的请求？

多个同类型的文件，可以合并。

下面两个文件，
`buttons.css`:

```
.green_button: {   color:  'green' }
.red_button: {   color:  'red' }
.yellow_button: {   color:  'yellow' }
```

`normalize.css`:

```
p { color: black  }
div { border:  0px; margin: 0px }
```

就可以合并成一个大文件：


```
.green_button: {   color:  'green' }
.red_button: {   color:  'red' }
.yellow_button: {   color:  'yellow' }
p { color: black  }
div { border:  0px; margin: 0px }
```

就是很简单的合并到一起，有冲突也没关系。 哪个出现在下方，哪个就生效么。

javascript也是一样的。

## Rails中，是如何给多个js/css合并到一起的？

### 对于js文件：使用 application.js

全名：`app/assets/javascripts/application.js`

原来的代码：

```
<script src="./static/javascript/jquery.min.js"></script>
<script src="./static/javascript/jqueryui.min.js"></script>
<script src="./static/javascript/bootstrap.min.js"></script>
```

我在rails当中，往往把所有的js文件，都放到 `app/assets/javascripts` 目录下。

然后, 在对应的erb页面中：
```
<%= javascript_include_tag 'jquery.min.js' %>
<%= javascript_include_tag 'jqueryui.min.js' %>
<%= javascript_include_tag 'bootstrap.min.js' %>
```

在Rails 3.0以后， Rails提供了新的功能: `assets pipeline`, 我们就把上面的代码，统一写到
`app/assets/javascripts/application.js` 中：

```

//= require jquery.min.js
//= require jqueryui.min.js
//= require bootstrap.min.js
```

注意： `//= require <文件名>`这个格式是固定的。 多个空格都不行！

然后，在布局文件`app/views/layouts/application.html.erb`中， 引用它：



```

<html>
<head>
  <%= javascript_include_tag 'application' %>
</head>
  ...
</html>

```

上面代码,在 "开发模式"下,就可以正确显示出我们引用的javascript

### 对于 css 的合并

把

```
<link rel="stylesheet" href="./static/buttons.css">
<link rel="stylesheet" href="./static/normalize.css">
<link rel="stylesheet" href="./static/font-awesome/css/font-awesome.css">
```

中的 css文件，保存到 `app/assets/stylesheets`目录下。

新增一个总体的 css文件： `app/assets/stylesheets/application.css`

内容如下

```
/*
 *= require buttons.css
 *= require normalize.css
 *= require font-awesome.css
 */
```

注意： 上面的 `*= require 文件名`的格式是固定的。多个空格都不行。

然后，在 布局文件 `app/views/layouts/application.html.erb`中， 引用它：


```
<html>
<head>
  <%= stylesheet_link_tag 'application' %> </head>
  ...
</html>

```

## 如何压缩css/js ？需要一个命令


```
$ bundle exec rake assets:precompile RAILS_ENV=production
```

- `rake assets:precompile`: 压缩所有的css, js, 以及为图片增加后缀。(方便服务器缓存)
- `RAILS_ENV=production`: 指定你的环境

这个命令还会耗时大约几分钟。 根据你的CSS/JS文件的数量， 时间上有所不同。

压缩完之后，就会发现， `public/assets` 目录下，多出来两个文件，形如：

```
application-308d70d0bd03d91770479da196ec0827.css
application-308d70d0bd03d91770479da196ec0827.js
```

这两个文件, 就是把所有的js, css压缩到一起的文件了. 以后, 我们每次打开WEB页面时, 只发起这
两个请求就够了

开发模式下，我们的页面加载，需要很多个 js， css 文件：
```
<link href="/assets/bootstrap.min.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/customized_bootstrap.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.core.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.theme.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.accordion.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/select2.min.css?body=1" media="all" rel="stylesheet" />
...
<script src="/assets/jquery.js?body=1"></script>
<script src="/assets/jquery_ujs.js?body=1"></script>
<script src="/assets/jquery.ui.core.js?body=1"></script>
<script src="/assets/jquery.ui.widget.js?body=1"></script>
<script src="/assets/jquery.ui.accordion.js?body=1"></script>
<script src="/assets/jquery.ui.position.js?body=1"></script>
<script src="/assets/jquery.ui.menu.js?body=1"></script>
```

在生产模式下，

```
<link href="/assets/application-ee1cf5e49ea54cc7b4cf3bef3be67d0c.css" media="all" rel="stylesheet" />
<script src="/assets/application-d45534ab0cd8c659530e2bebd7e60fbe.js"></script>
```


## rake assets:precompile 需要在什么时候运行？

1. 不需要每次部署都运行。
2. 只在 production 环境下，才需要运行。
3. 只在修改了css/js文件后，才需要运行。


## asset_path 与 public 目录

Rails 2.x的时代， 在 asset pipeline的概念出来之前，所有的图片，js，css 都放到`public` 目录下。

Rails 3.1 以后（2011年），出现了 `asset_pipeline` , 那么所有的需要进行 js，css压缩
的内容， 都要放到`app/asset`

所有的 `app/assets` 目录下的东西，都可以被编译。（ `32.jpg` -> `32_a1b2c3d4...z100.jpg`)

通过页面中,加入这个,就可以看到:
```
<%= asset_path '2.png' %>
```

所有的 public 目录下的东西，都可以在开发模式下，被rails服务器所访问。
例如：

`public/images/1.jpg`

在页面index.html.erb中,就可以访问到:

```
<img src='images/1.jpg'/>
```

## 为什么不鼓励，把erb中使用的图片，放到`public` 目录下？

部署时，几乎所有的 `/images`,  `/assets`, 都会被nginx做解析。
nginx中的解析，会跟rails中直接引用 `images/1.jpg` 有一些冲突。

所以，在目前看来， asset pipeline 极度流行， 那么大家不要把erb（css）中
用到的图片，放到 `public` 目录下。

而且在rails 服务器的生产环境下,配置让rails 服务器来解析对于assets的请求,很麻烦.

## 一个例外

上传的文件，都保存到 public 目录。

