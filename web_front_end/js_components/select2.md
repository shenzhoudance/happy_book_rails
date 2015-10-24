# jQuery select2下拉菜单

Select2提供一个可定制的下拉选择框,支持搜索、标签、远程数据集,已经无限滚动等许多其他可使用的选项。


# 使用方法一：当gem一样的使用

1. 安装select2-rails gem,在gemfile文件中添加如下代码，并运行bundle install:
```
gem "select2-rails"
```

2. 在app/assets/javascripts/application.js文件中添加如下代码:
```
//= require select2
```

3. 在app/assets/stylesheets/application.css文件中添加如下代码:
```
*= require select2
```

# 使用方法二：直接在页面中使用

1. 在html页面的head标签中，添加如下代码:
```html
<link href="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.0/css/select2.min.css" rel="stylesheet" />
<script src="//cdnjs.cloudflare.com/ajax/libs/select2/4.0.0/js/select2.min.js"></script>
```

2. 在页面的script标签中，这样写：
```html
<script type="text/javascript">
  $('select').select2();
</script>
```

# 实例

## 常规选择框

1. 基本样式：每一个分组下，有n个可选标签，其中分组项不可选。
```html
<select class="js-states form-control">
  <% @provinces.each do |province| %>
    <optgroup label='<%= province.name %>'>
    <% @provinces.each_with_index do |city, index| %>
      <option value='<%= index %>'> <%= city.name %> </option>
        <% if @provinces.length == index %>
        </optgroup>
      <% end %>
    <% end %>
  <% end %>
</select>
```
![图1.0](http://image.happysoft.cc/image/209/1.0.png)

2. 带选择框的基本样式：在基本样式的基础上，增加了搜索框，在选项较多的情况下可以进行搜索。
```html
<select class="js-example-basic-single" style='width:300px'>
  <% @provinces.each do |province| %>
    <optgroup label='<%= province.name %>'>
    <% @provinces.each_with_index do |city, index| %>
      <option value='<%= index %>'> <%= city.name %> </option>
      <% if @provinces.length == index %>
        </optgroup>
      <% end %>
    <% end %>
  <% end %>
</select>
<script>
  $(".js-example-basic-single").select2();
</script>
```
![图1.1](http://image.happysoft.cc/image/210/1.1.png)

## 多选样式：多值选择框，支持同时选择多个选项。
```html
<select class="js-example-basic-multiple" multiple="multiple" style='width:300px'>
  <% @provinces.each do |province| %>
    <optgroup label='<%= province.name %>'>
    <% @provinces.each_with_index do |city, index| %>
      <option value='<%= index %>'> <%= city.name %> </option>
      <% if @provinces.length == index %>
        </optgroup>
      <% end %>
    <% end %>
  <% end %>
</select>
<script>
  $(".js-example-basic-multiple").select2();
</script>
```
![图2.0](http://image.happysoft.cc/image/211/2.0.png)

## 带默认值下拉选择框
1. 下拉基本样式
```html
<select class="js-example-placeholder-single" style='width:300px'>
  <% @provinces.each do |province| %>
    <optgroup label='<%= province.name %>'>
    <% @provinces.each_with_index do |city, index| %>
      <option value='<%= index %>'> <%= city.name %> </option>
      <% if @provinces.length == index %>
        </optgroup>
      <% end %>
    <% end %>
  <% end %>
</select>
<script>
$(".js-example-placeholder-single").select2({
  placeholder: "Select a state",
  allowClear: true
});
</script>
```
![图3.0](http://image.happysoft.cc/image/212/3.1.png)

2. 下拉多选样式
```html
<select class="js-example-placeholder-multiple" style='width:300px' multiple="multiple">
  <% @provinces.each do |province| %>
    <optgroup label='<%= province.name %>'>
    <% @provinces.each_with_index do |city, index| %>
      <option value='<%= index %>'> <%= city.name %> </option>
      <% if @provinces.length == index %>
        </optgroup>
      <% end %>
    <% end %>
  <% end %>
</select>
<script>
$(".js-example-placeholder-multiple").select2({
  placeholder: "Select a state"
});
</script>
```
![图3.1](http://image.happysoft.cc/image/213/3.2.png)

## 带图片的选项

```html
function formatState (state) {
  if (!state.id) { return state.text; }
  var $state = $(
      '<span><img src="vendor/images/flags/' + state.element.value.toLowerCase() + '.png" class="img-flag" /> ' + state.text + '</span>'
      );
  return $state;
};
$(".js-example-templating").select2({
  templateResult: formatState
});
```
![图4.0](http://image.happysoft.cc/image/214/4.0.png)

更多实例请参考官方文档https://select2.github.io/.
