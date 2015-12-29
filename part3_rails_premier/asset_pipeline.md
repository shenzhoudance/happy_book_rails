# assets pipeline

```
  <%= javascript_include_tag 'application' %>
  <% if @is_decorating_case_show %>
    <%= javascript_include_tag 'jquery.min' %>
    <%= javascript_include_tag 'jquery.imgbox.pack' %>
    <%= javascript_include_tag 'jquery.jeditable' %>
  <% end %>
```


## 1. 如果新增 js 文件？

application.js

```
//= require jquery
//= require jquery_ujs
//= require jquery.ui.all
//= require jquery-ui
```

把你要增加的js文件，放到这里就可以了。

## 2. 尽量不要在 layout 文件中， 使用 针对某个model的条件判断。
一个例外：  <%= if current_user ... %>

## 3. 不要把js 文件单独写出来。

## 4. jquery.min  与 前面的 jquery.js 是重复的。
jquery.js :  1.11.1    2014年
jquery.min.js:   1.3    2009年

很可能会出问题的。

## js 太分散了。


rake assets:precompile


开发模式下，我们的页面加载，需要很多个 js， css 文件：
```
  <link href="/assets/bootstrap.min.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/customized_bootstrap.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.core.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.theme.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.accordion.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.menu.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.autocomplete.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.button.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.datepicker.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.resizable.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.dialog.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.progressbar.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.selectable.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.slider.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.spinner.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.tabs.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.tooltip.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.base.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/jquery.ui.all.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/select2.min.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/select2-bootstrap.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/validate.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/application.css?body=1" media="all" rel="stylesheet" />
<link href="/assets/lrtk.css?body=1" media="all" rel="stylesheet" />
  <script src="/assets/jquery.js?body=1"></script>
<script src="/assets/jquery_ujs.js?body=1"></script>
<script src="/assets/jquery.ui.core.js?body=1"></script>
<script src="/assets/jquery.ui.widget.js?body=1"></script>
<script src="/assets/jquery.ui.accordion.js?body=1"></script>
<script src="/assets/jquery.ui.position.js?body=1"></script>
<script src="/assets/jquery.ui.menu.js?body=1"></script>
<script src="/assets/jquery.ui.autocomplete.js?body=1"></script>
<script src="/assets/jquery.ui.button.js?body=1"></script>
<script src="/assets/jquery.ui.datepicker.js?body=1"></script>
<script src="/assets/jquery.ui.mouse.js?body=1"></script>
<script src="/assets/jquery.ui.draggable.js?body=1"></script>
<script src="/assets/jquery.ui.resizable.js?body=1"></script>
<script src="/assets/jquery.ui.dialog.js?body=1"></script>
<script src="/assets/jquery.ui.droppable.js?body=1"></script>
<script src="/assets/jquery.ui.effect.js?body=1"></script>
<script src="/assets/jquery.ui.effect-blind.js?body=1"></script>
```

在生产模式下，

```
  <link href="/assets/application-ee1cf5e49ea54cc7b4cf3bef3be67d0c.css" media="all" rel="stylesheet" />
  <script src="/assets/application-d45534ab0cd8c659530e2bebd7e60fbe.js"></script>
```

优势：  增加任意的js， css，都不会影响到发送的request 的次数。


2015年前的新浪： 打开一次页面，需要 150多个请求（70多个css， 50个js）
