# HTML 简介

## HTML是 XML的一种.

可以认为所有对HTML的 语法要求,其实跟XML是一样的.

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>我是一个HTML页面</title>
  </head>
  <body>
    <p>hi , HTML! </p>
  </body>
</html>
```

## 基本语法

### 标签结构

所有的页面部分都是由标签组成, 格式是: `<` + 名字 + `>`, 例如: `<div>`, `<html>`, `<p>`.

标签要有开始, 闭合. 两种形式:
`<div></div>`,  `<hr/>`

开始,闭合的顺序务必统一, 例如:
```html
<div> <p> hello </p> </div>
```

不可以写成:
```html
<div> <p> hello </div> </p>
```

### attribute 与 text

下面的例子中, `style` 就是attribute, `你好!`就是 text.
```html
<div style='float:left'> 你好! </div>
```

### 书写格式

虽然对层次结构没有要求,但是强烈建议大家在书写时,做好格式的对齐.
否则看代码时会吐血:
例如:
```html
<div>
    <p> hello
</p>
    </div>
```
p.s. 为了避免这个问题, yaml产生了.

### 常用标签

- div/span

两者作用几乎一样, 除了在布局方面, div默认是display:block , span默认是 display: inline

- table, tr, td/th

tr 代表一行, td/th代表一列, 例如:下面是一个一行两列的table:
```html
<table>
  <tr>
    <td></td>
    <td></td>
  </tr>
</table>
```

- head/title/meta

用来表示 该网页的标题等信息,例如 :

```html
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <title>Titanium中国开发者论坛 </title>
  </head>
```

- style

为页面增加一段script, 例如:

```html
<link href="/assets/application.css" media="screen" rel="stylesheet" type="text/css" />
```

- script

可以引用一个外部的js文件,也可以直接把js代码写进去.
```html
<script src="/assets/application.js" type="text/javascript"></script>
<script>
  alert('hi, javascript!');
</script>
```
- img
图片

```html
<img src='apple.jpg' />
```

- a
链接

```html
<a href='http://tidev.in'> Titanium 中文论坛</a>
```

- ol/ul/li

需要配合使用, ul = unordered list, ol = ordered list,  是有序或者无序列表,
目前被大量用在实现菜单上. 例如:

```html
<ul>
  <li>首页</li>
  <li>关于我们</li>
</ul>
```

- 表单组件 form/input/select

当我们需要用户注册时,就需要用户提交一个表单(form), 表单中有各种组件,例如: input(文本输入框),
多行文本输入框(textarea), 下拉单(select), 单选(radio), 多选(checkbox).
普通的按钮是 button
提交时使用 submit
TODO : select 例子

```html
<form action='/register' method='POST' >
  手机: <input type='text' name='mobile_phone'/> <br/>
  性别: <input type='radio' name='sex' value='male'/>男
        <input type='radio' name='sex' value='female'/>女<br/>
  头像: <input type='file' name='avatar' /><br/>
  爱好: <input type='checkbox' value='game' name='hobbies' />游戏
        <input type='checkbox' value='basketball' name='hobbies' />篮球
        <input type='checkbox' value='football' name='hobbies' />足球<br/>

  简历: <input type='textarea' style='height:100px; width: 300px' name='introduction' /><br/>
  <input type='submit' value='提交'/>
</form>
```
在浏览器中看起来是:

![表单例子](http://image.happysoft.cc/image/10/form_example.jpg)

这个form会以 POST的请求方式, 发送到 /register 这个url上.  我们就可以在对应的后台代码中进行处理了.
