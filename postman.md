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
