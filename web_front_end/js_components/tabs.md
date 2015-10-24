#Web tabs(标签页)

##Jquery tabs

> 参考文章: https://jqueryui.com/tabs

很多网页都需要菜单栏，经常是被定义在`<ui>`里面，下面这里用的是*jquery ui*的一个组件来制作tabs，使用方式比较简单:

- 引入对应的 jquery.js jquery-ui的文件:

```html
  <link rel="stylesheet" href="http://code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
  <script src="http://code.jquery.com/jquery-1.10.2.js"></script>
  <script src="http://code.jquery.com/ui/1.11.4/jquery-ui.js"></script>
```
> 这里的版本不固定，只是作为参考例子展示,也可以将js或者css文件下载到本地，不引用远程的。

- 定义好`<ui><li>..`的菜单

- 在`<script>`中调用`tabs()`的方法如下所示:

```
 $(function() {
    $( "#tabs" ).tabs();
  });
```
下面是一个简单的实例代码:

```html

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>jQuery UI Tabs - Default functionality</title>
  <link rel="stylesheet" href="http://code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
  <script src="http://code.jquery.com/jquery-1.10.2.js"></script>
  <script src="http://code.jquery.com/ui/1.11.4/jquery-ui.js"></script>
  <script>
  $(function() {
    $( "#tabs" ).tabs();
  });
  </script>
</head>
<body>
  <div id="tabs">
    <ul>
      <li><a href="#tabs-1">标签一</a></li>
      <li><a href="#tabs-2">标签二</a></li>
      <li><a href="#tabs-3">标签三</a></li>
    </ul>
    <div id="tabs-1">
      <p>我是标签一的内容</p>
      <p>欢迎您!!!</p>
    </div>
    <div id="tabs-2">
      <p>我是标签二的内容</p>
    </div>
    <div id="tabs-3">
      <p>我是标签三的内容</p>
    </div>
  </div>
</body>
</html>

```

上述代码实现的效果如下图所示:

![tabs.gif](/images/tabs.gif)

这时候，一个简单的tabs便制作完成了。

##bootstrap tabs

> 参考文献: http://getbootstrap.com/getting-started

有时候需要一些漂亮点的tabs，如bootstrap样式的tabs。接下来下面以rails为例子，看看使用tabs的流程:

> 此处只是单纯的引用bootstrap的文件，并没有使用gem，rails本身有很多优秀的bootstrap gem可供使用。

- 导入bootstrap文件
    
	- 下载[bootstrap](http://getbootstrap.com/getting-started/)的包文件,打开里面的zip包解压查看内容
	- 将里面的*js/bootstrap.min.js*复制到rails项目中*app/assets/javascripts*目录下面
	- 将*css/bootstrap.min/css* *css/bootstrap.theme.min.css*复制到rails项目中*app/assets/stylesheets* 目录下面
	- 将*font*文件夹复制到rails项目的*app/assets*目录下面
	- 在*app/assets/javascripts/application.js* 添加代码:

	```
	//= require bootstrap.min
	```
	- 在*app/assets/stylesheets/application.css* 添加代码:

	```
	*= require bootstrap.min
	*= require bootstrap-theme.min
	```
	
- 增加一个简单的标签页
	
	添加代码到*app/views/welcome/index.html.erb*中:
	
	```
	<div>
		<ul class="nav nav-tabs">
  			<li class="active"><a href="#">Home</a></li>
  			<li><a href="#">Menu 1</a></li>
  			<li><a href="#">Menu 2</a></li>
  			<li><a href="#">Menu 3</a></li>
		</ul>
	<div>
	```
	
	这时候一个简单的标签页就做好了。如图所示:
	
	![simple_tabs.png](/images/simple_tabs.png)
	
- 下面是有二级选项的标签页
  
  效果如图所示:
  
  ![tabs.png](/images/tabs.png)
  
  我们只需要添加代码到*app/views/welcome/index.html.erb*文件的`<body>`中即可:
  
  ```
  <ul class="nav nav-tabs">
  		<li class="active"><a href="#">Home</a></li>
  		<li class="dropdown">
    		<a class="dropdown-toggle" data-toggle="dropdown" href="#">Menu 1
    		<span class="caret"></span></a>
    		<ul class="dropdown-menu">
      			<li><a href="#">Submenu 1-1</a></li>
      			<li><a href="#">Submenu 1-2</a></li>
      		<li><a href="#">Submenu 1-3</a></li> 
    		</ul>
  		</li>
  		<li><a href="#">Menu 2</a></li>
  		<li><a href="#">Menu 3</a></li>
</ul>
  ```
  
> 这种方式也适用于普通的html页面，只需要替换导入bootstrap的方式就行。