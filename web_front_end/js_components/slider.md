#轮播图
轮播图，在web开发时，经常是用的到，很普遍很常见。我们可以利用jquery来实现它。

##步骤
###下载jquery的js文件

首先在你的html代码中添加你的jquery文件，不管是普通的.html文件还是rails项目中的.html.erb文件，第一步都得把jquery的js文件先放到自己的项目里，这样相比在代码里直接通过url请求远程的js文件在稳定性和页面加载速度上的都会很大的优势。所以，我们可以先去jquery的官网下载对应的js文件，放置在项目对应的文件夹里。

###添加对文件的引用

rails项目和一般的web项目是有些不同的，在引用的时候。

rails项目中：

+ 先把要引用的js文件放在`app/assets/javascripts`下

+ 然后打开`app/assets/javascripts/application.js`

+ rails是通过`//=require jquery`这样的方式来引用同目录下的jquery.js文件。我们就可以如下添加：

```ruby
//=require jquery
//=require jquery.banner
```

+ 当然rails可以在html代码中直接添加引用的代码。

普通html文件中：

+ 在代码中直接通过标签对来引用，首先你可以把js文件任意放在你项目中的文件夹中，为了方便可读，我们放在js文件夹中。

+ 然后在你的代码中添加你的文件引用

```html
<script type="text/javascript" src="js/jquery.js"></script>
<script type="text/javascript" src="js/jquery.banner.js"></script>

```

##设置样式
这里因为我们只下载引用了jquery的js文件，所以我们需要自己想要的样式的话，就需要自己设置。同样我们可以把设置样式的代码提取到一个css文件中，再去html代码中引用，也可以直接在html中直接写。这里我们就直接写在代码,在代码中添加自己的设置，可以如下这样：

```html
<style type="text/css">
*{margin:0;padding:0;list-style-type:none;}
a,img{border:0;}
body{font:12px/180% Arial, Helvetica, sans-serif, "新宋体";}

.banner{width:100%;overflow:hidden;height:470px;position:relative}
.banList{position:absolute;left:50%;margin-left:-960px;height:470px}
.banList li{height:470px;opacity:0;position:absolute;transform:scale(0);transition:transform 0.5s ease 0s, opacity 1.5s ease 0s;z-index:1;}
.banList li.active{opacity:1;transform:scale(1);z-index:2;}
.fomW{position:absolute;bottom:20px;left:50%;height:20px;z-index:9;width:1000px;margin-left:-500px}
.jsNav{text-align:center;}
.jsNav a{display:inline-block;background:#fff;width:15px;height:15px;border-radius:50%;margin:0 5px;}
.jsNav a.current{background:#fc8f0f;cursor:pointer}
</style>
```

##添加布局
我们接下来继续在html中设置这个轮播图的布局：

```html
<div class="banner">
  <ul class="banList">
    <li class="active"><img src="images/img1.jpg"/></a></li>
    <li><img src="images/img2.jpg"/></a></li>
    <li><img src="images/img3.jpg"/></a></li>
  </ul>
  <div class="fomW">
    <div class="jsNav">
      <a href="javascript:;" class="trigger current"></a>
      <a href="javascript:;" class="trigger"></a>
      <a href="javascript:;" class="trigger"></a>
    </div>
  </div>
</div>
```

其中的img1.jpg，img2.jpg，img3.jpg可以替换成你的图片文件名称，也可以引用远程图片。


##添加方法

在body/body标签内要添加方法来实现滑动。

```html
<script type="text/javascript">
$(function(){
  $(".banner").swBanner();
});
</script>
```

这样，这个简单的轮播图就算完成了。
