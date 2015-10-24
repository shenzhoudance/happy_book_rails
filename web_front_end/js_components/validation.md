#表单验证
>表单验证又分为前台验证和后台验证，在前台我们使用的jQuery 的validate插件,这个插件的好处就是绑定了一
系列的验证，同时还能自己去编写，非常容易使用。

![](/images/jqueryvalidate.gif)
##安装
1)可以把jQuery validate文件下载到本地，然后再HTML中引用
```html
<script src='../jquery.js' type="text/javascript"/>
<script src='../jquery.validate.js' type="text/javascript"/>
```
2)直接在HTML中引用CDN
```html
<script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
<script src='http://ajax.aspnetcdn.com/ajax/jquery.validate/1.14.0/jquery.validate.js' type="text/javascript"/>
```
##使用
我们先来看一段代码
```html
<script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
<script src='http://ajax.aspnetcdn.com/ajax/jquery.validate/1.14.0/jquery.validate.js' type="text/javascript" ></script>
<%= form_for :user,url:users_path,:html=>{:id=>"form"} do |f| %>
<div>用户名<%= f.text_field:name %></div>
<div>密码  <%= f.password_field:password  %></div>
<div>邮箱  <%= f.text_field:mail  %></div>
<%=f.submit "signup"%>
<% end %>
<style>
input.error { border: 1px solid red; }
label.error {
background:url("./demo/images/unchecked.gif") no-repeat 0px 0px;
           padding-left: 16px;
           padding-bottom: 2px;
           font-weight: bold;
color: #EA5200;
}
label.checked {
  background:url("./demo/images/checked.gif") no-repeat 0px 0px;
}
</style>
<script>
$("#form").validate({
  rules:{
    "user[name]":"required",
    "user[password]":{
    "required":true,
    "rangelength":[6,10]
    },
    "user[mail]":"email"
  },
  messages:{
    "user[name]":"请输入用户名",
    "user[password]":{
    "required":"请输入密码",
    "rangelength":"密码在6-10位"
    },
    "user[mail]":"请注意邮箱格式"
  }
});
</script>

```
由于插件的默认错误信息是英语，所以在这里我们选择自己在js文件中写检验规则。
form就是你要检验的表单，rules中是检验规则，而messages中是错误信息,rules和messages通过name属性来控制的,id和class不可用。

*本章末有默认检验规则

##常见问题
###错误信息显示位置
默认是显示在该元素后面,修改的话使用下边这种方法
```javascript
errorPlacement: function(error, element) {
    error.appendTo(element.parent());
    }
```
###更改错误信息的样式
我们可以自定义错误信息的样式
```javascript
input.error { border: 1px solid red; }
label.error {
  background:url("./demo/images/unchecked.gif") no-repeat 0px 0px;
  padding-left: 16px;
  padding-bottom: 2px;
  font-weight: bold;
  color: #EA5200;
}
label.checked {
  background:url("./demo/images/checked.gif") no-repeat 0px 0px;
}
```
###添加自定义验证规则
写在additional-methods.js文件中(没有就自己创建并引用)或 jquery.validate.js文件中。
```javascript
addMethod：name, method, message
```
```javascript
// 邮政编码验证
jQuery.validator.addMethod("isZipCode", function(value, element) {
    var tel = /^[0-9]{6}$/;
    return this.optional(element) || (tel.test(value));
}, "请正确填写您的邮政编码");
```
##默认检验规则
|序号|规则|描述|
|----|----|----|
|1|required:true|  必须输入的字段。|
|2|remote:"check.php" | 使用 ajax 方法调用 check.php 验证输入值。|
|3|email:true|  必须输入正确格式的电子邮件。|
|4|url:true|  必须输入正确格式的网址。|
|5|date:true| 必须输入正确格式的日期。日期校验 ie6 出错，慎用。|
|6|dateISO:true|  必须输入正确格式的日期（ISO），例如：2009-06-23，1998/01/22。只验证格式，不验证有效性。|
|7|number:true| 必须输入合法的数字（负数，小数）。|
|8|digits:true| 必须输入整数。|
|9|creditcard:| 必须输入合法的信用卡号。|
|10|equalTo:"#field"| 输入值必须和 #field 相同。|
|11|accept:| 输入拥有合法后缀名的字符串（上传文件的后缀）。|
|12|maxlength:5| 输入长度最多是 5 的字符串（汉字算一个字符）。|
|13|minlength:10|  输入长度最小是 10 的字符串（汉字算一个字符）。|
|14|rangelength:[5,10]|  输入长度必须介于 5 和 10 之间的字符串（汉字算一个字符）。|
|15|range:[5,10]|  输入值必须介于 5 和 10 之间。|
|16|max:5|输入值不能大于 5。|
|17|min:10| 输入值不能小于 10。|
