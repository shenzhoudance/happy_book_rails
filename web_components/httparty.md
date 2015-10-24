# 发送 HTTP RESTFul 请求 send http request

主要靠：  httparty 这个gem 来搞定。

RESTful

HTTP 的几种请求方法（http request methods)

refer to:  http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html

这只是个 规格说明，用来建议各大browser厂商所遵循，而事实上的类型目前只有GET, POST。

restful中所使用的仅仅是：GET, POST, PUT, DELETE。

可以认为HTTP 请求，分成四类：

- GET： 发起请求，不改变服务器状态。例如查看某个用户，某个订单
- POST ：新建数据, 需要在服务器上做新建操作。例如新建一个订单。
- PUT ： 修改数据, 例如：修改一个用户的名字。
- DELETE ： 删除数据. 例如：删掉一个用户。


值得一提的是， 在发送request的时候，一般都会有一个header. 例如：

```
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Encoding:gzip, deflate, sdch
Accept-Language:zh-CN,zh;q=0.8,en;q=0.6
```

目前，浏览器中事实上存在的只有GET 和 POST两种请求。其他的请求（例如PUT, DELETE)
需要在客户端中模拟，并且在Rails框架中解析才算做实现。

## 安装

$ gem install httparty

## 发起一个 GET请求

```ruby
require 'httparty'
response = HTTParty.get 'www.baidu.com'
puts response.body, response.code, response.headers
```

## 发起请求，带headers

```ruby
require 'httparty'
response = HTTParty.get 'www.baidu.com',  headers: {"User-Agent" => APPLICATION_NAME}
puts response.body, response.code, response.headers
```

## 发起POST 请求

```ruby
class Partay
  include HTTParty
  base_uri 'http://localhost:3000'
end

options = {
  body: {
    pear: { # your resource
      foo: '123', # your columns/data
      bar: 'second',
      baz: 'last thing'
    }
  }
}

pp Partay.post('/pears.xml', options)
```

## 使用 代理服务器

```ruby
class Foo
  include HTTParty
  http_proxy 'http://foo.com', 80, 'user', 'pass'
end
```
## 使用timeout

timeout（超时）特别重要。默认一般是30秒，你可以把它缩短成5秒甚至更少。 

```ruby
# -*- encoding : utf-8 -*-
class StaticFilesController < ApplicationController
  include HTTParty
  default_timeout 3

  def get_generator_plans
    self.class.get 'some/url'
  end
end
```

## 一个例子： 抓取并且分析远程的接口的数据

以抓取 PPTV 在 豌豆荚的首页 的排名为例子。

这个接口就是： http://112.5.16.36:3011/wdj/http://startpage.wandoujia.com/api/v1/fetch?f=phoenix2&max=5&netStatus=WIFI&net=WIFI×tamp=1434693333387&id=wandoujia_android&v=4.17.1&u=7139c1b794754b9a957557a21f024fd7ba26b4ca&launchedCount=31&start=0&token=34ce2e167a88c88cc53a842665c06690&entry=other§ionItemNum=3&vc=6708&ch=wx_baidu_mm_float

用ruby 模拟这个过程：

```ruby
require 'httparty'
url  = 'http://112.5.16.36:3011/wdj/http://startpage.wandoujia.com/api/v1/fetch?f=phoenix2&max=5&netStatus=WIFI&net=WIFI×tamp=1434693333387&id=wandoujia_android&v=4.17.1&u=7139c1b794754b9a957557a21f024fd7ba26b4ca&launchedCount=31&start=0&token=34ce2e167a88c88cc53a842665c06690&entry=other§ionItemNum=3&vc=6708&ch=wx_baidu_mm_float'
response = HTTParty.get(url)

# 把response.body 转换成JSON对象。
result =  JSON.parse(response.body)

# 通过观察，发现该接口返回的数据中, cards是个节点。包含了我们要抓取的内容。所以，
result['cards'].each_with_index do |e, index|
    # 这里就可以获得 PPTV 所在的位置了。
    puts "#{e['feedItem']['title']}, index: #{index}"
end
```

下面是结果：

```bash
口袋成语, index: 0
中华英雄传, index: 1
我爱封神, index: 2
KK唱响, index: 3
.....
PPTV聚力, index: 49
QQ浏览器, index: 50
...
```

注意:

1.  对于某些接口,需要用汉字转换到 unicode , 见:  http://pages.ucsd.edu/~dkjordan/resources/unicodemaker.html

2. 对于 url, 有时候转到的是原始URL, 有的是 编码后的URL, 在这里也可以转换: http://meyerweb.com/eric/tools/dencoder/

