# 动画效果

jQuery animate()允许创建自定义动画。

## 语法

```
.animate({params}, speed, callback);
```
- 必须的params参数定义形成动画的css属性。
- 可选的speed参数规定效果的时长。它允许的取值：‘slow’，‘fast’或者毫秒值。
- 可选的callback参数是动画完成后做执行的函数名称。

```
.animate(properties[,duration][,easing][,complete])
```
- properties : css属性和值的对象。
- duration : 动画执行的时长。
- easing : 指定过渡的函数。
- complete : 动画执行方法。

## 示例

### 移动元素的位置：

```
$("button").click(function() {
  $("div").animate({left: '280px'});
});
```
注意：如果要对元素的位置进行操作，首先要记得把元素的css position属性设置为relative、fixed或者absolute。

### 操作元素的多个属性：

```
$("button").click(function() {
  $("div").animate({
    left: '280px,
    opacity: '0.5',
    height: '150px',
    width: '150px',
  });
});
````

### 使用相对值操作元素的属性：

```
$("button").click(function(){
  $("div").animate({
    left:'250px',
    height:'+=150px',
    width:'+=150px'
  });
});
```

### 动画的队列功能：
顺序的执行一系列动画。
```
$("button").click(function(){
    var div=$("div");
    div.animate({height:'300px',opacity:'0.4'},"slow");
    div.animate({width:'300px',opacity:'0.8'},"slow");
    div.animate({height:'100px',opacity:'0.4'},"slow");
    div.animate({width:'100px',opacity:'0.8'},"slow");
    });
```


























