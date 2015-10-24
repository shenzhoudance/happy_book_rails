# Tooltips(提示框)

##Jquery Tooltips

> 项目地址: https://jqueryui.com/tooltip/

使用jquery给输入框组件增加提示方式非常简单，只需要三个步骤即可:

- 引用`jquery ui`和`jquery`相关的js和css文件:

	```html
  <link rel="stylesheet" href="http://code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
  <script src="http://code.jquery.com/jquery-1.10.2.js"></script>
  <script src="http://code.jquery.com/ui/1.11.4/jquery-ui.js"></	script>
```
- 在`<script>`中添加jquery语法:

	```
	$(function() {
    	$( document ).tooltip();
   });
	```
- 给输入框空间加入`title`属性就可以了

下面是一个小的例子:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <link rel="stylesheet" href="http://code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
  <script src="http://code.jquery.com/jquery-1.10.2.js"></script>
  <script src="http://code.jquery.com/ui/1.11.4/jquery-ui.js"></script>
  <script>
  $(function() {
     $( document ).tooltip();
  });
  </script>
</head>
<body>
	<input title="我是一个提示框">
</body>
</html>
```
提示框效果图如下:

![tooltips_left](/images/tooltips.gif)

##Bootstrap Tooltips

> ref: http://getbootstrap.com/javascript/#tooltips

有时候需要给提示框加上bootstrap的样式的时候，则需要改变一下引用方法和参数了。boostrap的效果图如下:

![tooltips_left](/images/tooltips_left.png)
	
![tooltips_right](/images/tooltips_right.png)

做法也非常简单，主要分为两个步骤：

- 导入bootstrap,用[Tabs](tabs.html)(	标签页)中的导入方式

- 在页面中添加代码如下即可:

```html
<h1 style="margin-bottom: 50px;">bootstrap tooltips</h1>

<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="left" title="Tooltip on left" style="margin-left: 50px;">Tooltip on left</button>
<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="top" title="Tooltip on top">Tooltip on top</button>
<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="bottom" title="Tooltip on bottom">Tooltip on bottom</button>
<button type="button" class="btn btn-default" data-toggle="tooltip" data-placement="right" title="Tooltip on right">Tooltip on right</button>
<script>
  $(function () {
    $('[data-toggle="tooltip"]').tooltip()
  })
</script>
```