# 百度地图

百度地图web端使用JavaScript编写的，适用网页中嵌入地图服务，支持2D/3D、卫星、街景、室内实景及定制化地图展现。

## 首先展示一个简单的实例“helloword”
```html
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
    <style type="text/css">
      body, html,#b-map {width: 90%;height: 90%;overflow: hidden;margin:10px auto 0 auto;}
    </style>
    <script type="text/javascript" src="http://api.map.baidu.com/api?type=quick&ak=IYogR4PrOzNklhaIAPby3EEq&v=1.0"></script>
    <title>显示地图</title>
  </head>
  <body>
    <div id="b-map"></div>
  </body>
</html>
<script type="text/javascript">
  var map = new BMap.Map("b-map");
  map.centerAndZoom(new BMap.Point(116.470, 39.926), 14);
</script>
```
![例子](http://image.happysoft.cc/image/217/map_helloword.png)

## 详细步骤：

### 每一个html页面，都应该有正确的文档类型声明。

```html
<!DOCTYPE html>
```

### 如果该页面需要在移动浏览器中也能正常显示，则需要在head标签中添加如下代码：

```html
<meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
```

### 下面设置让地图显示在浏览器窗口样式：

```html
<style type="text/css">
  body, html,#l-map {width: 90%;height: 90%;overflow: hidden;margin:10px auto 0 auto;}
</style>
```

### 引用百度地图API文件，需要引入：

```html
<script type="text/javascript" src="http://api.map.baidu.com/api?type=quick&ak=你的密钥&v=1.0"></script>
```
申请ak请访问：http://lbsyun.baidu.com/apiconsole/key。

### 创建地图实例:

```
var map = new BMap.Map('b-map');
```
通过new操作符可以创建一个地图实例，其参数可以是元素的id也可以是元素对象。

### 创建点坐标：

```
var point = new BMap.Point(116.470, 39.926);
```
point类描述了一个地理坐标点，其中116.470表示经度，39.926表示纬度。

### 初始化地图，创建地图实例之后，需要初始化：

```
map.centerAndZoom(point, 14);
```

## 地图的缩放与移动
### 移动地图方法

```
map.enableDragging();
```
该方法默认开启，即无论是否设置改方法，都可以通过手指拖动地图来实现地图的移动。

```
map.setCenter(new BMap.Point(116.470, 39.926));
map.panTo(new BMap.Point(116.470, 39.926));
```
这两个方法，可以通过改变地图的中心点实现地图的移动。

### 缩放地图方法

```
map.addControl(new BMap.ZoomControl());
```
地图上可以通过双指缩放地图外，还可以通过添加缩放控件，操作缩放控件实现地图的缩放。
![缩放例子](http://image.happysoft.cc/image/218/map_suofang.png)

## 地图控件

### 缩放、比例尺控件

  地图上的控件包括：缩放控件、比例尺控件和自定义控件。可以通过addcontrol、removecontrol方法添加和删除控件。

1. 直接在地图上添加缩放、比例尺控件：

```
var map = new BMap.Map("b-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 14);
var zoomControl=new BMap.ZoomControl();
map.addControl(zoomControl);//添加缩放控件
var scaleControl=new BMap.ScaleControl();
map.addControl(scaleControl);//添加比例尺控件
```

2. 删除缩放、比例尺控件：

```
map.removeControl(zoomControl);//删除缩放控件
map.removeControl(scaleControl);  //删除比例尺控件
```

### 自定义控件

1. 定义构造函数并继承control

```
// 定义一个控件类，即function
function ZoomControl(){
  // 设置默认停靠位置和偏移量
  this.defaultAnchor = BMAP_ANCHOR_TOP_LEFT;
  this.defaultOffset = new BMap.Size(10, 10);
}
// 通过JavaScript的prototype属性继承于BMap.Control
ZoomControl.prototype = new BMap.Control();
```

2. 初始化自定义控件

```
// 自定义控件必须实现initialize方法，并且将控件的DOM元素返回
// 在本方法中创建个div元素作为控件的容器，并将其添加到地图容器中
ZoomControl.prototype.initialize = function(map){
// 创建一个DOM元素
var div = document.createElement("div");
// 添加文字说明
div.appendChild(document.createTextNode("放大2级"));
 // 设置样式
 div.style.cursor = "pointer";
 div.style.border = "1px solid gray";
 div.style.backgroundColor = "white";
 // 绑定事件，点击一次放大两级
 div.onclick = function(e){
         map.setZoom(map.getZoom() + 2);
         }
         // 添加DOM元素到地图中
         map.getContainer().appendChild(div);
            return div;
            }
```

3. 添加自定义控件:

```
// 创建控件实例
var myZoomCtrl = new ZoomControl();
// 添加到地图当中
map.addControl(myZoomCtrl);
```

## 地图覆盖物

### 地图覆盖物概述

1. overlay：覆盖物的抽象基类，所有的覆盖物均继承次方法。
2. marker：标注表示地图上的点，可自定义标注的图标。
3. polyline：表示地图上的折线。
4. infowindow：信息窗口也是一种特殊的覆盖物，它可以展示更为丰富的文字和多媒体信息。注意：同一时刻只能有一个信息窗口在地图上打开。

### 标注

标注表示地图上的点。

下面的示例向地图中心店添加了一个标注，并使用默认的标注样式。
```
var map = new BMap.Map("l-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 14);
var marker = new BMap.Marker(new BMap.Point(116.404, 39.915));        // 创建标注
map.addOverlay(marker);
```
![标注例子](http://image.happysoft.cc/image/219/map_biaozhu.png)

### 信息窗口

信息窗口在地图上方的浮动显示html内容。信息窗口可直接在地图上的任意位置打开，也可以在标注对象上打开。

```
var opts = {
width : 100,     // 信息窗口宽度
        height: 50,     // 信息窗口高度
        title : "Hello"  // 信息窗口标题
}
var infoWindow = new BMap.InfoWindow("World", opts);  // 创建信息窗口对象
map.openInfoWindow(infoWindow, marker.getPosition());      // 打开信息窗口
```

### 折线

polyline表示地图上的折线覆盖物。它包含一组点，并将这些点连接起来形成折线。

```
var polyline = new BMap.Polyline([new BMap.Point(116.399, 39.910),
    new BMap.Point(116.405, 39.920)],
    {strokeColor:"blue", strokeWeight:6, strokeOpacity:0.5});
map.addOverlay(polyline);
```

## 地图事件

浏览器中的JavaScript是“事件驱动的”，这表示JavaScript通过生成事件来响应交互，并期望程序能够“监听”感兴趣的活动。

### 事件监听

addEventListener方法有两个参数：监听的事件名称和事件触发时调用的函数。在下面示例中，每当用户点击地图时，会弹出一个警告框。

```
var map = new BMap.Map("l-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("click", function(){
       alert("您点击了地图。");
       });
```

通过监听事件还可以捕获事件触发后的状态。下面示例显示用户拖动地图后地图中心的经纬度信息。

```
var map = new BMap.Map("l-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("moveend", function(){
       var center = map.getCenter();
             alert("地图中心点变更为：" + center.lng + ", " + center.lat);
             });
```

### 事件参数和this

在标准的DOM事件模型中（DOM Level 2 Events），监听函数会得到一个事件对象e，在e中可以获取有关该事件的信息。同时在监听函数中this会指向触发该事件的DOM元素。 百度地图API的事件模型与此类似，在事件监听函数中传递事件对象e，每个e参数至少包含事件类型（type）和触发该事件的对象（target）。

例如，通过参数e得到点击的经纬度坐标。

```
var map = new BMap.Map("b-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("click", function(e){
       alert(e.point.lng + ", " + e.point.lat);
       });
```

或者通过this得到地图缩放后的级别。

```
var map = new BMap.Map("b-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
map.addEventListener("zoomend", function(){
       alert("地图缩放至：" + this.getZoom() + "级");
       });
```

### 移除监听事件

当你不再希望监听事件时，可以将事件监听进行移除。

```
var map = new BMap.Map("b-map");
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);
function showInfo(e){
   alert(e.point.lng + ", " + e.point.lat);
      map.removeEventListener("click", showInfo);
      }
      map.addEventListener("click", showInfo);
```

更多地图的操作请参考官方文档。
API 极速版 ：http://developer.baidu.com/map/index.php?title=jsextreme
API 大众版 ：http://developer.baidu.com/map/index.php?title=jspopular/guide/introduction
地图Demo : http://developer.baidu.com/map/jsdemo-mobile.htm
参考类 : http://developer.baidu.com/map/reference/index.php

