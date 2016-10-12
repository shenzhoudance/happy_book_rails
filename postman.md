# 调试接口的工具

我们每天写接口， 需要两个调试用的东西：

1. 发送各种请求
2. 展示各种请求

仅仅使用chrome是不够的。 因为：浏览器的地址栏，只能发送GET请求。
JSON内容在浏览器中，也不会自动的格式化。 看起来乱糟糟的。

所以，我们要使用对应的工具，来提高效率。

## POSTMan

他是google chrome的插件。 使用它，可以发送任意类型（GET/POST/PUT/DELETE)的请求。还可以设置任意的
header, 总之，功能非常强大。

步骤：

1. 你要有个翻墙工具。 （否则进不了chrome store)
2. google postman （我搜到的地址是 https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop )
3. 打开。
4. 点击右上角的 "add to chrome "按钮。并且确认。
5. 安装好了之后， 就可以看到： chrome的 应用程序列表（  chrome://apps/ ） 中，就可以看到了对应的图标：
6. 点击图标，运行：
7. 点击“ skip this, go stranght to the app"

## 使用方式：

1. 地址栏输入 url, 选择好请求的类型（GET  ， POST） ，点击“SEND”
2. 就可以看到内容了。


## 发起POST请求

1. 选择请求的类型为POST， 输入URL ，
在body中， 选择 "form-data", 填好 key, value. 例如： name  李磊

## JSON View (格式化JSON的内容）

JSON 文件，默认是乱乱的， 没有任何缩进。(连换行都没有）  例如：

```
{"civil_cities":[{"initial":"热门城市","cities":[{"id":1018,"name":"北京","code":1},{"id":1153,"name":"上海","code":2},{"id":1058,"name":"广州","code":32},{"id":1154,"name":"深圳","code":30}]},{"initial":"A","cities":[{"id":191,"name":"阿坝","code":1838},{"id":192,"name":"阿克苏","code":173},{"id":193,"name":"阿勒泰","code":175},{"id":194,"name":"阿里","code":97},{"id":195,"name":"安康","code":171},{"id":196,"name":"安庆","code":177},{"id":197,"name":"鞍山","code":178},{"id":198,"name":"安顺","code":179},{"id":199,"name":"安阳","code":181}]},{"initial":"B","cities":[{"id":201,"name":"白城","code":1116},{"id":202,"name":"百色","code":1140},{"id":203,"name":"白沙","code":21025},{"id":204,"name":"白山","code":3886},{"id":205,"name":"白银","code":1541},{"id":206,"name":"保定","code":185},{"id":207,"name":"宝鸡","code":112},{"id":208,"name":"保山","code":197},{"id":209,"name":"包头","code":141},{"id":210,"name":"巴彦淖尔","code":3887},{"id":211,"name":"巴中","code":3966},{"id":212,"name":"北海","code":189},{"id":213,"name":"北京","code":1},{"id":214,"name":"蚌埠","code":182},{"id":215,"name":"本溪","code":1155},{"id":216,"name":"毕节","code":22031},{"id":217,"name":"滨州","code":1820},{"id":218,"name":"亳州","code":1078}]},{"initial":"C","cities":[{"id":219,"name":"昌江","code":
```

所以，当一个文件一大，是无法肉眼识别的。

所以，我们要使用工具。 例如： json view.

### 安装

1. google:  json view
2. 会得到一个地址： https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc?hl=ko
3. 点击 'Add to Chome' 并且确定。
4. 安装好之后， 刷新即可。

### 如果 没有翻墙工具怎么办？

那就使用 在线格式化工具。 例如： json.cn 一看就会用，不说了。

但是，不如json view的chrome 插件管用。
