# 标签

一个jQuery UI插件，来处理多标签字段。

## 使用方法

### 添加jQuery和jQuery UI插件：

```
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.5.2/jquery.min.js" type="text/javascript" charset="utf-8"></script>
<script src="https://ajax.googleapis.com/ajax/libs/jqueryui/1.8.12/jquery-ui.min.js" type="text/javascript" charset="utf-8"></script>
<script src="js/tag-it.js" type="text/javascript" charset="utf-8"></script>
```

### 添加样式文件：

```
<link rel="stylesheet" type="text/css" href="http://ajax.googleapis.com/ajax/libs/jqueryui/1/themes/flick/jquery-ui.css">
<link href="css/jquery.tagit.css" rel="stylesheet" type="text/css">
```

### 一个helloword：

```
<script type="text/javascript">
  $(document).ready(function() {
      $("#tags").tagit();
      });
</script>

<h1><font color="#99d">Minimal</font></h1>
<div style="width:300px;">
  <ul id="tags"></ul>
</div>
</ul>
```

## demo和方法:

### 自动补全功能：

- 用数组的方式，设置可选值：
```
$("#tags").tagit({
    availableTags: ["ios", "java", "php", "javascript", "ruby", "python", "c"]
    });
```

- 自定义自动补全：
```
$(function() {
  var availableTags = ["asp", "ajax", "java", "javascript", "php", "ruby", "python"];
  $("#tags").autocomplete({
    source: availableTags
  });
});
<input id="tags">
</input>
```

### singleFieldTags

- 使用ul标签实现：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">singleFieldTags</font></h1>
  <input name="tags" id="mySingleField" value="Apple, Orange" disabled="true">
  <ul id="singleFieldTags"></ul>
</div>
$('#singleFieldTags').tagit({
  availableTags: availableTags,
  singleField: true,
  singleFieldNode: $('#mySingleField')
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

- 使用input标签实现：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">singleFieldTags2</font></h1>
  <input name="tags" id="singleFieldTags2" value="Apple, Orange">
</div>
$('#singleFieldTags2').tagit({
  availableTags: availableTags
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

### 使用回车实现分隔而不是空格：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">Spaces Allowed Without Quotes</font></h1>
  <ul id="allowSpacesTags"></ul>
</div>
$('#allowSpacesTags').tagit({
  availableTags: availableTags,
  allowSpaces: true
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

### 带有默认值：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">Preloading Data in Markup</font></h1>
  <ul id="myTags">
    <li>Tag1</li>
    <li>Tag2</li>
  </ul>
</div>
$('#myTags').tagit({
  availableTags: availableTags,
  itemName: 'item',
  fieldName: 'tags'
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

### 只读模式的：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">read only</font></h1>
  <ul id="readOnlyTags">
    <li>Mark1</li>
    <li>Mark2</li>
  </ul>
</div>
$('#readOnlyTags').tagit({
  readOnly: true
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

### 移除标签：

```
<div style="width:300px; margin:0 auto;">
  <h1><font color="#99d">Remove Confirmation</font></h1>
  <ul id="removeTags">
    <li>remove 1</li>
    <li>remove 2</li>
  </ul>
</div>
$('#removeTags').tagit({
  availableTags: availableTags,
  removeConfirmation: true
});
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

更多示例请参考：http://aehlke.github.io/tag-it/examples.html












