> TODO:  
> 
 - 例子不完整，没有写明怎么引用In-Place Editor插件
 - `body`中怎么写，有一个gif图会更好看点。
 - 参数 cancel_button ：不知道是不是引入一行代码

#inplace editor

inplace editor顾名思义就是原地编辑器。很多时候我们需要通过点击文本来对文本进行就地编辑。这样在用户体验上是种不错的选择，我们现在就来看看这个东西。

我们这里用到的In-Place Editor是一个Jquery插件。

##特征

+ 通过`ESC`键来取消编辑

+ 当编辑框失去焦点的时候，默认会把当前编辑框中的值提交到server

+ 它会把值提交给一个回调函数


##用法

要实现这个其实很简单，可以看下面这个例子，把in-place editor应用在一个class叫‘name’的元素上，下面展示了它所有的参数。

```javascript
$(".name").editInPlace({
  url: "http://com.examplesite.www/users",
  params: "name=david"
});
```

一旦提交了，会把下面三个参数也以post的形式传送给url。

+ original_html:原先的文本值

+ update_value:新的文本值

+ 使用了in-place editor 的元素id


##参数

下面是部分它提供的参数：

+ callback(function):默认在编辑完成的时候是没有回调函数。

+ cancel_button(string):默认 <input type="”submit”" class="”inplace_cancel”" value="”Cancel”">
</input>

+ default_text (string):默认如果方法中`string`为空时，会显示 “(Click here to add text)”


##具体例子可以参考下面：
```javascript
$(document).ready(function(){

  // 下面这些例子都是提交到一个回调函数.
  // 如果你想提交到一个url的话，就把注释的那一行取消注释

  // 下面这个是最基本的使用inPlaceEditor
  $("#editme1").editInPlace({
    callback: function(unused, enteredText) { return enteredText; },
    // url: './server.php',
    show_buttons: true
  });

  // 这个例子是展示如何如何回调方法并且不用那种固定的文本框来显示。
  // 还可以设置其它的一些选项，比如按行列来保存一个图片，和滚动完成时的颜色。
  $("#editme2").editInPlace({
    callback: function(unused, enteredText) { return enteredText; },
    // url: "./server.php",
    bg_over: "#cff",
    field_type: "textarea",
    textarea_rows: "15",
    textarea_cols: "35",
    saving_image: "./images/ajax-loader.gif"
  });

  // 一个我们可以限定选项的选择输入框
  $("#editme3").editInPlace({
    callback: function(unused, enteredText) { return enteredText; },
    // url: "./server.php",
    field_type: "select",
    select_options: "Change me to this, No way:no"
  });

  // 通过一个回调方法来更新两个div
  $("#editme4").editInPlace({
    callback: function(original_element, html, original){
      $("#updateDiv1").html("The original html was: " + original);
      $("#updateDiv2").html("The updated text is: " + html);
      return(html);
    }
  });

  $("#editme5").editInPlace({
    saving_animation_color: "#ECF2F8",
    callback: function(idOfEditor, enteredText, orinalHTMLContent, settingsParams, animationCallbacks) {
      animationCallbacks.didStartSaving();
      setTimeout(animationCallbacks.didEndSaving, 2000);
      return enteredText;
    }
  });

  // 如果你想移除绑定
  // > $(selectorForEditors).unbind('.editInPlace')

  // 如果要移除所有的事件，你需要用到'closed'方法。

});

```
