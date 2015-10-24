#树状图（jsTree）
很多时候我们需要给一些内容以树状的图的效果来展示，这里我们就可以通过jsTree来实现。

jsTree是一个jquery的插件。非常棒的开源项目。

##步骤

###下载jsTree
可以去官网下载jsTree的资源包：https://www.jstree.com/
所有我们用的资源，都在压缩包中的`/dist`文件夹中。

###添加jsTree的主题

```html
<link rel="stylesheet" href="dist/themes/default/style.min.css" />
```

###设置一个容器div

```html
<div id="jstree_demo_div"></div>
```

###引用jquery文件

```html
<script src="dist/libs/jquery.js"></script>
```

###引用jsTree文件

```html
<script src="dist/jstree.min.js"></script>
```

###创建实例

```html
$(function () { $('#jstree_demo_div').jstree(); });
```

###添加监听事件

```html
$('#jstree_demo_div').on("changed.jstree", function (e, data) {
  console.log(data.selected);
  });
```

###与你的实例交互

```html
$('button').on('click', function () {
    $('#jstree').jstree(true).select_node('child_node_1');
    $('#jstree').jstree('select_node', 'child_node_1');
    $.jstree.reference('#jstree').select_node('child_node_1');
    });
```

##最终的代码大致如下：
```html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>jsTree test</title>
    <!-- 2 load the theme CSS file -->
    <link rel="stylesheet" href="dist/themes/default/style.min.css" />
  </head>
  <body>
    <!-- 3 setup a container element -->
    <div id="jstree">
      <!-- in this example the tree is populated from inline HTML -->
      <ul>
        <li>Root node 1
          <ul>
          <li id="child_node_1">Child node 1</li>
          <li>Child node 2</li>
          </ul>
        </li>
        <li>Root node 2</li>
      </ul>
    </div>
    <button>demo button</button>

    <!-- 4 include the jQuery library -->
    <script src="dist/libs/jquery.js"></script>
    <!-- 5 include the minified jstree source -->
    <script src="dist/jstree.min.js"></script>
    <script>
    $(function () {
        // 6 create an instance when the DOM is ready
        $('#jstree').jstree();
        // 7 bind to events triggered on the tree
        $('#jstree').on("changed.jstree", function (e, data) {
          console.log(data.selected);
          });
        // 8 interact with the tree - either way is OK
        $('button').on('click', function () {
          $('#jstree').jstree(true).select_node('child_node_1');
          $('#jstree').jstree('select_node', 'child_node_1');
          $.jstree.reference('#jstree').select_node('child_node_1');
          });
        });
    </script>
  </body>
</html>
```
