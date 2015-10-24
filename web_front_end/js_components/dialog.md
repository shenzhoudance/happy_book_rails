#弹出窗口(dialog)

弹出窗口(dialog)是一种比较常见的内容表现形式，这种内容呈现的方式在给予我们提醒信息以及呈现某种特定信息时具有很好的表达效果。这一小节我们就来介绍一下如何完成一个简单的弹出窗口界面。

##创建一个简单的弹出窗口

下面就是一个简单的弹出窗口的代码实现。

首先我们建立一个简单的html页面,然后引入相关的jquery文件和css文件，这里我使用的是网上的资源，但是我个人还是建议大家下载相应的源码，然后在本地引用。然后编写相应的jquery方法，实现点击弹出窗口的效果。那些动画效果同样来自于jquery方法，有兴趣的同学可以自己尝试去掉那些
效果。（就是function中的show和hide的属性）

```html
<!doctype html>
  <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>jQuery UI Dialog - Default functionality</title>
      <link rel="stylesheet" href="http://code.jquery.com/ui/1.11.4/themes/smoothness/jquery-ui.css">
      <script src="http://code.jquery.com/jquery-1.10.2.js"></script>
      <script src="http://code.jquery.com/ui/1.11.4/jquery-ui.js"></script>
      <link rel="stylesheet" href="http:/resources/demos/style.css">
      <script>
      $(function() {
        $( "#dialog" ).dialog({
          autoOpen:false,
          show: {
            effect: "blind",
            duration: 1000
          },
          hide: {
            effect: "explode",
            duration: 1000
          }
        });

        $( "#opener" ).click(function() {
          $( "#dialog" ).dialog('open');
        });
      });
    </script>
    </head>
    <body>
      <div id="dialog" title="Basic dialog">
      <p>这时一个简单的弹出窗口页面，该页面可以被移动，改变大小，关闭。</p>
      </div>
    </body>
    <button id="opener">点击我弹出窗口</button>
  </html>
```

**TODO：请尝试阐述一下你贴出来的代码的作用，以及相关重要的代码行的意义。贴出的代码没有任何注释，也不说明代码的作用，就仅仅是一堆代码，没有任何意义，失去了贴出这些代码的作用。最好能有一个代码的演示效果gif。**

更多的资料大家可以参阅<https://jqueryui.com>
