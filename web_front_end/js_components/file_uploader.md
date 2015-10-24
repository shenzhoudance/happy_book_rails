#文件和图片的上传功能

文件和图片上传功能现在已经是一个很重要的功能，在实际的网站等应用之中也拥有比较重的地位，这一小节我们将给大家介绍一下简单的文件上传实现。

###rails的API中一个简单的文件上传的方法file_field
***
####file_field方法  file_field(object_name,method,options = {})

返回一个文件上传的对话框选择适合上传的文件(由文件模版和方法决定)。更多的可选参数可以通过一个哈希(hash)传递给输入对话框，这些可选参数在后面会进行相应的说明。
配合form_for使用file_field方法我们将可以建立一个闭合的上传文件的表单。

####Options

-  创建标准的html属性标签
-  :disabled  当该属性设置为true时，用户将不能使用该输入框
-  :multiple  当该属性的值为true时，最新的浏览器将能够支持文件多选。
-  :accept    如果被设置为可以接受一个或者多个mime－types，用户再上传文件的时候就需要一定的删选，同时你也需要建立一个类型的验证机制。

####Examples

```ruby
file_field(:user, :avatar)
=><input type="file" id="user_avatar" name="user[avatar]" />

file_field(:post, :image, :multiple => true)
=><input type="file" id="post_image" name="post[image]" multiple="true" />

file_field(:post, :attached, accept: 'text/html')
=><input accept="text/html" type="file" id="post_attached" name="post[attached]" />

file_field(:post, :image, accept: 'image/png,image/gif,image/jpeg')
=><input type="file" id="post_image" name="post[image]" accept="image/png,image/gif,image/jpeg" />

file_field(:attachment, :file, class: 'file_input')
=><input type="file" id="attachment_file" name="attachment[file]" class="file_input" />
```

这里还有一些比较好用的jquery插件可以实现文件的上传功能，这里简单介绍一款jquery File Upload的插件
详细信息可以参阅[这些文档](https://github.com/blueimp/jQuery-File-Upload/wiki/Basic-plugin)

下面是一些示例代码。
---
-   简单快速的安装使用
```html
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="utf-8">
    <title>jQuery File Upload Example</title>
  </head>
  <body>

    <p>构建html代码</p>
    <input id="fileupload" type="file" name="files[]" data-url="server/php/" multiple>

    <p>引入核心文件</p>
    <script src="http://libs.useso.com/js/jquery/jquery/1.9.1/jquery.min.js"></script>
    <script src="js/vendor/jquery.ui.widget.js"></script>
    <script src="js/jquery.iframe-transport.js"></script>
    <script src="js/jquery.fileupload.js"></script>

    <p>写入JS初始化</p>
    <script>
      $(function () {
      $('#fileupload').fileupload({
      dataType: 'json',
      done: function (e, data) {
      $.each(data.result.files, function (index, file) {
      $('<p/>').text(file.name).appendTo(document.body);
      });
      }
      });
      });
    </script>
  </body>
</html>
```
在上面的示例代码中，我们看到使用的json数据类型，服务器返回的也是以json数据格式解析的。实际中，我们可能需要根据自己的需要处理响应式的html内容或者其他的数据类型，这样我们就需要自己
完成时回调自定义的处理函数。

-   让上传时显示进度条
fileupload进度条的触发事件来源于单个或者多个上传文件的事件，通过预设绑定或者插件的形式都可以实现时间的出发。
进度条的动态显示原理时通过计算的百分比数据来改变bar的宽度实现的，同时我们使用css来控制bar的高度。
```javascript
$('#fileupload').fileupload({
    /* ... */
progressall: function (e, data) {
var progress = parseInt(data.loaded / data.total * 100, 10);
$('#progress .bar').css(
  'width',
  progress + '%'
  );
}});
```
```css
<div id ="progess">
  <div class = "bar" style = "width: 0%;"></div>
</div>
.bar {
   height: 18px;
   background: bule;
}
```

-   如何显示是上传中还是上传完毕的状态
通常情况下，我们上传一个文件时我们都要知道其上传状态，如何把相对应的上传状态传递给用户呢？
这里的基本原理是在上传时和完成后分别调用方法来显示。

```javascript
$(function () {
    $('#fileupload').fileupload({
      dataType: 'json',
      add: function (e, data) {
      data.context = $('<p/>').text('Uploading...').appendTo(document.body);
      data.submit();
      },
      done: function (e, data) {
      data.context.text('Upload finished.');
      }
  });
});
```

-   添加开始按钮
在上面的实现中都是选中文件直接上传的，但是大多数时候我们需要选中之后点击开始上传之后上传文件，我们该怎么实现呢？
其实我们只要在data.context中加入html代码和相应的按钮绑定事件就可以了。

```javascript
$(function () {
    $('#fileupload').fileupload({
        dataType: 'json',
        add: function (e, data) {
        data.context = $('<button/>').text('Upload')
        .appendTo(document.body)
        .click(function () {
          data.context = $('<p/>').text('Uploading...').replaceAll($(this));
          data.submit();
          });
        },
        done: function (e, data) {
        data.context.text('Upload finished.');
        }
  });
});
```

在我本地的完整代码实现为
```html
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="utf-8">
    <title>jQuery File Upload Example</title>
  </head>
  <body>

    //构建html代码
    <input id="fileupload" type="file" name="files[]" data-url="server/php/" multiple>

    //引入核心文件
    <script src="/Users/logic/workspace/software/jQuery-File-Upload/js/jquery-1.9.1.js"></script>
    <script src="/Users/logic/workspace/software/jQuery-File-Upload/js/vendor/jquery.ui.widget.js"></script>
    <script src="/Users/logic/workspace/software/jQuery-File-Upload/js/jquery.iframe-transport.js"></script>
    <script src="/Users/logic/workspace/software/jQuery-File-Upload/js/jquery.fileupload.js"></script>

    <div id ="progess">
      <div class = "bar" style = "width: 0%;"></div>
    </div>
    <style>
    .bar {
       height: 18px;
       background: blue;
     }
   </style>
    //写入JS初始化
    <script>
      $(function () {
        $('#fileupload').fileupload({
          dataType: 'json',
          add: function (e, data) {
            data.context = $('<button/>').text('Upload')
              .appendTo(document.body)
              .click(function () {
                data.context = $('<p/>').text('Uploading...').replaceAll($(this));
                data.submit();
              });
            },
            done: function (e, data) {
              data.context.text('Upload finished.');
            },
            progressall: function (e, data) {
              var progress = parseInt(data.loaded / data.total * 100, 10);
              $('#progress .bar').css(
              'width',
              progress + '%'
              );
            }});
        });
    </script>
  </body>
</html>
```

到这里就是基本的jquery file uploader插件的使用方法，更多的实现大家可以直接查阅[这些文档](https://github.com/blueimp/jQuery-File-Upload/wiki/Basic-plugin)
