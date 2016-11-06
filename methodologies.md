# 花多少时间来搜索合适？

1. 先自己试着搜索1～2小时. 我的正常搜索时间： 5～ 20分钟。 少数内容， 2小时内也基本搞定。
2. 如果2小时还搞不定，果断人肉问身边的同事同学。
3. 如果他还不知道，咱们就继续问别人。起码问3个。
4. 这时候， 别人起码能给到你很多线索。 再google/baidu.
5. 如果2小时后还不行，就上stackoverflow / tieba /zhidao /zhihu 上 发问。 重要问题: 给额外的分数
（例如在stackoverflow上，别人回答一个题目，得10分。 如果你分数多，就可以设置bonus , 最高可以给到
500）

我问过几个问题，90%都可以顺利在 stackoverflow上得到答案。 如果得不到的话，可能该问题就无解了。

# 官网上的内容比我的内容多的多？ 该怎么办？ 比如log4r 的官网有好多内容。

http://log4r.rubyforge.org/manual.html

咱们的： http://web.siwei.tech/web_components/log4r.html

80-20定律。  最有用的内容只有20%，但是它用在80%的地方，

所以，我们的策略：

1. 需求导向。 （我能解决实际的需求问题就行，其他的80%的内容，如果用不上，那就不学）

上面也回答了： 什么学，什么不学。 （看需求）

2. 在什么情况下学？
平时是不用学的。啥时候，老板说： 男主角，把这个需求作一下。  这个时候才学。

# 怎么学？  需求导向的话， 要作的事儿看起来是一个点，但是官网中有一堆东西。该怎么定位？

1. 如何定位？

不要根据官网来定位。 信息海量。除非你的英语阅读能力很强，经验老道。

使用google. 例如： 使用ruby on rails如何上传文件。

那么，我要作的，不是到rails官网看， 而是： google:

'rails file upload'

```
Ruby on Rails File Uploading - TutorialsPoint
https://www.tutorialspoint.com/ruby...rails/rails-file-uploading.ht...
You may have a requirement in which you want your site visitors to upload a file on your server. Rails makes it very easy to handle this requirement. Now we will ...
ruby - Uploading a file in Rails - Stack Overflow
stackoverflow.com/questions/14174044/uploading-a-file-in-rails

2013. 1. 5. - While there are plenty of gems that solve file uploading pretty nicely (see https://www.ruby-toolbox.com/categories/rails_file_uploads for a list), rails ...
How to upload a file in ruby on rails? - Stack Overflow
stackoverflow.com/.../how-to-upload-a-file-in-ruby-on-rails

2012. 7. 5. - I'm very new in ruby on rails. I'm stuck with a problem. I want to make a ... Thank you for example, I study rails too! It works in rails 3.1. My code:
Form Helpers — Ruby on Rails Guides
guides.rubyonrails.org/form_helpers.html
What makes a file upload form different. How to post forms to external resources and specify setting an authenticity_token . How to build complex forms.
```

所以，本着经验，stackoverflow的内容，是最精准的。 所以，我们点开的，不是官方网站

stackoverflow 上有两个问题， 一个问题15个星，一个问题35个星。 后者有62个回答。 而且
内容最简单： （超级简单）

于是，我就知道了。

官方网站就不用看了。

TODO: 好几个图。放到 methodologies 目录下。
# 如何搜索到这些知识？ (上面回答了，google）

# 大师是否一开始也看官网说明？ (不看）

对于： 自己丝毫不了解的东西，我会先找到官方网站， 看看LOGO啥的， 找到文档的链接，看看hello world.
让自己心里有个底，知道这个东西的最权威的文档在哪里。

然后，我就去搜索。google.  不做就会死的事儿。
这时候没有时间一行一行的看代码。 就只能google.
google完之后， 自己试。  （80%都是行的， 20%的情况会失败，往往是自己本地，没有这个环境）

例如：  redis cache file

我就搜，然后，找到一个看起来最正确的答案（因为有很多么）亲手试。
但是发现不行：

```
while time.time() - t - expiry < 0:
  yield r.get(fpKey)

So 1 roundtrip per iteration. With the function, you have:

if r.exists(fpKey):
  return r.get(fpKey)
```

不知道上面代码写在什么 地方（英语里叫context ), 所以这个时候，回到官方文档， 查看intro...
查看如何 搭建环境。。。跑起redis ... 之后，慢慢就知道， 上面的代码应该放到什么位置了。

# 知道如何用它，但是不知道如何提升/深入。 (看官方文档，或者源代码）

例如上面的，file upload, 官方文档中，专门有一段：

```
5.1 What Gets Uploaded
The object in the params hash is an instance of a subclass of IO. Depending on the size of the uploaded file it may in fact be a StringIO or an instance of File backed by a temporary file. In both cases the object will have an original_filename attribute containing the name the file had on the user's computer and a content_type attribute containing the MIME type of the uploaded file. The following snippet saves the uploaded content in #{Rails.root}/public/uploads under the same name as the original file (assuming the form was the one in the previous example).
```

# 是否每个软件都有裙带性？ ==  是否每个工具都有依赖关系？

是的。 但是完全不影响我们的使用。 （在java/oc/c中，到处都是import, include, 在ruby中，我们感受不到）

例子：

rails.  会依赖好多好多：  activesupport, active


    rails (4.2.1)
      actionmailer (= 4.2.1)
      actionpack (= 4.2.1)
      actionview (= 4.2.1)
      activejob (= 4.2.1)
      activemodel (= 4.2.1)
      activerecord (= 4.2.1)
      activesupport (= 4.2.1)
      bundler (>= 1.3.0, < 2.0)
      railties (= 4.2.1)
      sprockets-rails


对于 activerecord, 依赖了：


    activerecord (4.2.1)
      activemodel (= 4.2.1)
      activesupport (= 4.2.1)
      arel (~> 6.0)

对于 activemodel, 依赖了：

    activemodel (4.2.1)
      activesupport (= 4.2.1)
      builder (~> 3.1)

对于  activesupport, 还有更多：

    activesupport (4.2.1)
      i18n (~> 0.7)
      json (~> 1.7, >= 1.7.7)
      minitest (~> 5.1)
      thread_safe (~> 0.3, >= 0.3.4)
      tzinfo (~> 1.1)

所以，这是个方法论： A  依赖 B， B 依赖 C， C 依赖 D， 如果D 依赖回A 呢？

上面的现象，在java当中很常见。 其他语言，也一样。


## 在操作系统中也是一样。

而且，还存在与：  Linux, Windows, Mac当中。 例如，Linux, 为什么会存在：  apt-get , yum  ...
在mac中， 为什么会存在： homebrew

## 编译好的包

Homebrew, apt-get , 它们都是 package manager (第三方包的管理工具）

$ apt-get install git,

它会自动帮我们把相关的内容下载下来。 省的我们一个一个去寻找了。

相关的内容有：

1. 依赖的第三方包
2. 编译后的代码（直接可以执行了）

上面的文件：
ubuntu/debian:    .deb
centos/redhat:    .rpm
mac:   .dmg
windows:  .exe (会自动解压，然后把编译后的文件，copy到对应目录下）

## 最后一个例子。

例如，我们的生产服务器，往往， 都是只有命令行。 没有GUI。
为什么我们的桌面LINUX有GUI呢？

因为，桌面LINUX， 比服务器LINUX，多了一些软件。（xwindow)

例如，  xwindow 可能有个组建，叫 font (字体）。 那么我安装这个font， 就要先安装 100个第三方包。

# 在ruby中，如何定位并且搜索？

例如：
$ gem install httparty

之后, 下面代码会报错：

```
require 'httparty'
response = HTTParty.get 'www.baidu.com'
puts response.body, response.code, response.headers
```

内容是：

```
liyun@hp:~/workspace/happy_book_rails$ ruby abc.rb
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `initialize': Connection refused - connect(2) for nil port 80 (Errno::ECONNREFUSED)
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `open'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `block in connect'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/timeout.rb:74:in `timeout'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:878:in `connect'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:863:in `do_start'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:852:in `start'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:1375:in `request'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/httparty-0.14.0/lib/httparty/request.rb:118:in `perform'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/httparty-0.14.0/lib/httparty.rb:560:in `perform_request'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/httparty-0.14.0/lib/httparty.rb:486:in `get'
	from /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/httparty-0.14.0/lib/httparty.rb:598:in `get'
	from abc.rb:2:in `<main>'
```
我们如何定位问题呢？  要可以看懂这个 出错信息（ error stack)

90%的语言(java, ruby, javascript... )， 都是从下到上的执行顺序。 （python: 从上到下看。。）

这个代码的执行顺序是从下往上：

1. 先找到  abc.rb 的第二行，
2. 在运行  httparty.rb 598 行
3.         httparty.rb 486 行

...
到最后， 执行的是 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb 的 879行：
出错信息是：

Connection refused - connect(2) for nil port 80 (Errno::ECONNREFUSED)

上面的出错信息，就是我要搜索的内容。

为了定位准确，（上面的消息跟ruby无关）, 我加上 文件名 http.rb .  所以，我最终搜索的是：

http.rb:879:in `initialize': Connection refused - connect(2) for nil port 80 (Errno::ECONNREFUSED)

结果： （截图）

发现第一个是push, 是gitlab 相关，我们不看（目测关联度极低）。
第二个结果： 跟ruby相关，果断点开， 发现是一个 rss 的问题。 跟httparty无关。 过。
第三个结果： 也是跟git相关。 跟httparty无关。 过。

反思： 搜索结果不准。 继续缩小范围。

于是，我加上 httpart , 搜索词换成了：

httparty http.rb:879:in `initialize': Connection refused - connect(2) for nil port 80 (Errno::ECONNREFUSED)

发现结果没太大变化。 因为： google认为， 我们的搜索词， 没啥变化。

所以，我们修改关键词。 （google 会自动忽略：  879 这样的数字，有时候会，有时候又不会，我们就碰）

所以, 我们现在这样搜：

httparty Connection refused - connect(2) for nil port 80 (Errno::ECONNREFUSED)

找到第二个：
http://stackoverflow.com/questions/11768111/connection-refused-connect2-httparty

里面的一个答案：

I was using an incorrect url.

于是我们就修改代码：


response = HTTParty.get 'www.baidu.com'

改成：


response = HTTParty.get 'http://www.baidu.com'


## httparty , 在ruby中报错，跟在rails中报错，是不一样的。

 rails增加了： 3个分类：
 1. Application Trace
 2. Framework trace
 3. Full trace


 1. Application Trace
app/controllers/books_controller.rb:8:in `index'

 2. Framework trace (增加了好多rails 框架的出错路径）

 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `initialize'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `open'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `block in connect'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/timeout.rb:74:in `timeout'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:878:in `connect'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:863:in `do_start'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:852:in `start'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:1375:in `request'
 httparty (0.14.0) lib/httparty/request.rb:118:in `perform'
 httparty (0.14.0) lib/httparty.rb:560:in `perform_request'
 httparty (0.14.0) lib/httparty.rb:486:in `get'
 httparty (0.14.0) lib/httparty.rb:598:in `get'
 actionpack (4.2.1) lib/action_controller/metal/implicit_render.rb:4:in `send_action'
 actionpack (4.2.1) lib/abstract_controller/base.rb:198:in `process_action'
 actionpack (4.2.1) lib/action_controller/metal/rendering.rb:10:in `process_action'
 actionpack (4.2.1) lib/abstract_controller/callbacks.rb:20:in `block in process_action'
 activesupport (4.2.1) lib/active_support/callbacks.rb:117:in `call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:117:in `call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:555:in `block (2 levels) in compile'
 activesupport (4.2.1) lib/active_support/callbacks.rb:505:in `call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:505:in `call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:92:in `_run_callbacks'
 activesupport (4.2.1) lib/active_support/callbacks.rb:776:in `_run_process_action_callbacks'
 activesupport (4.2.1) lib/active_support/callbacks.rb:81:in `run_callbacks'
 actionpack (4.2.1) lib/abstract_controller/callbacks.rb:19:in `process_action'
 actionpack (4.2.1) lib/action_controller/metal/rescue.rb:29:in `process_action'
 actionpack (4.2.1) lib/action_controller/metal/instrumentation.rb:32:in `block in process_action'
 activesupport (4.2.1) lib/active_support/notifications.rb:164:in `block in instrument'
 activesupport (4.2.1) lib/active_support/notifications/instrumenter.rb:20:in `instrument'
 activesupport (4.2.1) lib/active_support/notifications.rb:164:in `instrument'
 actionpack (4.2.1) lib/action_controller/metal/instrumentation.rb:30:in `process_action'
 actionpack (4.2.1) lib/action_controller/metal/params_wrapper.rb:250:in `process_action'
 activerecord (4.2.1) lib/active_record/railties/controller_runtime.rb:18:in `process_action'
 actionpack (4.2.1) lib/abstract_controller/base.rb:137:in `process'
 actionview (4.2.1) lib/action_view/rendering.rb:30:in `process'
 actionpack (4.2.1) lib/action_controller/metal.rb:196:in `dispatch'
 actionpack (4.2.1) lib/action_controller/metal/rack_delegation.rb:13:in `dispatch'
 actionpack (4.2.1) lib/action_controller/metal.rb:237:in `block in action'
 actionpack (4.2.1) lib/action_dispatch/routing/route_set.rb:74:in `call'
 actionpack (4.2.1) lib/action_dispatch/routing/route_set.rb:74:in `dispatch'
 actionpack (4.2.1) lib/action_dispatch/routing/route_set.rb:43:in `serve'
 actionpack (4.2.1) lib/action_dispatch/journey/router.rb:43:in `block in serve'
 actionpack (4.2.1) lib/action_dispatch/journey/router.rb:30:in `each'
 actionpack (4.2.1) lib/action_dispatch/journey/router.rb:30:in `serve'
 actionpack (4.2.1) lib/action_dispatch/routing/route_set.rb:819:in `call'
 rack (1.6.4) lib/rack/etag.rb:24:in `call'
 rack (1.6.4) lib/rack/conditionalget.rb:25:in `call'
 rack (1.6.4) lib/rack/head.rb:13:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/params_parser.rb:27:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/flash.rb:260:in `call'
 rack (1.6.4) lib/rack/session/abstract/id.rb:225:in `context'
 rack (1.6.4) lib/rack/session/abstract/id.rb:220:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/cookies.rb:560:in `call'
 activerecord (4.2.1) lib/active_record/query_cache.rb:36:in `call'
 activerecord (4.2.1) lib/active_record/connection_adapters/abstract/connection_pool.rb:649:in `call'
 activerecord (4.2.1) lib/active_record/migration.rb:378:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/callbacks.rb:29:in `block in call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:88:in `call'
 activesupport (4.2.1) lib/active_support/callbacks.rb:88:in `_run_callbacks'
 activesupport (4.2.1) lib/active_support/callbacks.rb:776:in `_run_call_callbacks'
 activesupport (4.2.1) lib/active_support/callbacks.rb:81:in `run_callbacks'
 actionpack (4.2.1) lib/action_dispatch/middleware/callbacks.rb:27:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/reloader.rb:73:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/remote_ip.rb:78:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/debug_exceptions.rb:17:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/show_exceptions.rb:30:in `call'
 railties (4.2.1) lib/rails/rack/logger.rb:38:in `call_app'
 railties (4.2.1) lib/rails/rack/logger.rb:20:in `block in call'
 activesupport (4.2.1) lib/active_support/tagged_logging.rb:68:in `block in tagged'
 activesupport (4.2.1) lib/active_support/tagged_logging.rb:26:in `tagged'
 activesupport (4.2.1) lib/active_support/tagged_logging.rb:68:in `tagged'
 railties (4.2.1) lib/rails/rack/logger.rb:20:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/request_id.rb:21:in `call'
 rack (1.6.4) lib/rack/methodoverride.rb:22:in `call'
 rack (1.6.4) lib/rack/runtime.rb:18:in `call'
 activesupport (4.2.1) lib/active_support/cache/strategy/local_cache_middleware.rb:28:in `call'
 rack (1.6.4) lib/rack/lock.rb:17:in `call'
 actionpack (4.2.1) lib/action_dispatch/middleware/static.rb:113:in `call'
 rack (1.6.4) lib/rack/sendfile.rb:113:in `call'
 railties (4.2.1) lib/rails/engine.rb:518:in `call'
 railties (4.2.1) lib/rails/application.rb:164:in `call'
 rack (1.6.4) lib/rack/lock.rb:17:in `call'
 rack (1.6.4) lib/rack/content_length.rb:15:in `call'
 rack (1.6.4) lib/rack/handler/webrick.rb:88:in `service'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/webrick/httpserver.rb:138:in `service'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/webrick/httpserver.rb:94:in `run'
 /home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/webrick/server.rb:294:in `block in start_thread'


3. fulltrace 同上， 只不过增加了application trace 的内容：

/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `initialize'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `open'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:879:in `block in connect'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/timeout.rb:74:in `timeout'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:878:in `connect'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:863:in `do_start'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:852:in `start'
/home/liyun/.rbenv/versions/2.2.1/lib/ruby/2.2.0/net/http.rb:1375:in `request'
httparty (0.14.0) lib/httparty/request.rb:118:in `perform'
httparty (0.14.0) lib/httparty.rb:560:in `perform_request'
httparty (0.14.0) lib/httparty.rb:486:in `get'
httparty (0.14.0) lib/httparty.rb:598:in `get'
app/controllers/books_controller.rb:8:in `index'
.... (以下同 framework trace )

# 如何使用？

我个人：

1. 直接看rails 给出的提示。  （包括HTML页面的提示，和 rails server 的提示）


```
Completed 500 Internal Server Error in 3ms (ActiveRecord: 0.0ms)

Errno::ECONNREFUSED (Connection refused - connect(2) for nil port 80):
  app/controllers/books_controller.rb:8:in `index'

  Rendered /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/actionpack-4.2.1/lib/action_dispatch/middleware/templates/rescues/_source.erb (4.9ms)
  Rendered /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/actionpack-4.2.1/lib/action_dispatch/middleware/templates/rescues/_trace.html.erb (2.0ms)
  Rendered /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/actionpack-4.2.1/lib/action_dispatch/middleware/templates/rescues/_request_and_response.html.erb (0.8ms)
  Rendered /home/liyun/.rbenv/versions/2.2.1/lib/ruby/gems/2.2.0/gems/actionpack-4.2.1/lib/action_dispatch/middleware/templates/rescues/diagnostics.html.erb within rescues/layout (17.4ms)
```

如果还没有思路， 那么直接看： full trace

# 对于一个新项目，如何着手做？

必须做到3点：

1. 要了解需求。
2. 要知道不同页面之间的跳转。
3. 要知道后台数据库的各个表之间的关系。

## 不了解需求怎么办？

多问。 不要憋着。
不要自己猜，不要自己发挥，要让最直接的人来回答问题。
不耻下问。忘掉玻璃心。(很多人，你问对方的时候，对方会不耐烦。为啥不耐烦？以为他已经给别人讲过N遍了。
我们要发扬 滚刀肉的精神， 必须问清楚。 带着很专业的态度。让它和自己心里明白，如果我不懂，项目没法作。

了解需求的时候， 必须有个可以留存的东西（文字，图片，语音）

做到一次弄明白。如果不明白的话，一定是对方的逻辑错了，或者没说明白，而不是我们忘了。

## 如果不熟悉页面之间的跳转，怎么办？

1. 自己亲手来画。页面的跳转关系。（加深印象）
2. 继续问。

## 如何理清后台数据库的表之间的关系？

画图。

1. 画出所有的表。 (标出表的名字和主要的列即可）
2. 不要画的漂亮。不要用尺子去比着画，而是 手绘，歪歪扭扭的，但是： 快，可以任意修改而不心疼。
3. 所有的表都画出来之后， 连接出各个表之间的关系。 标注出 那边是 1 ， 那边是多（ *也行， N 也行)

以上几点都做完之后， 那么，就容易了，直接把主要的表的CRUD做出来。


## 如何梳理需求？

1. 要了解 用例图。  把每个角色， 要干什么事儿，写出来。

学员  O    ----> 可以报名
     / \   ----> 可以查看课程分类
           ----> 可以查看课程详情
           ----> 可以注册


2. 要了解类图(class diagram). 画出class 之间的对应关系（不需要把它的所有属性都列出来）

3. 要熟练掌握：时序图（sequence diagram)

 3.1 列出所有的角色
 3.2 按照时间点，严格的一条一条的写，从上到下
 3.3 在 3.2的基础上，每个角色, 找到谁，干什么事儿，都写出来。


掌握上面三种UML图就可以了。特别是第三种，是最强大的分析问题，梳理流程的方法。

忘掉：山寨图。   国内的人, 特别是半吊子的人，最喜欢画， http://img5.imgtn.bdimg.com/it/u=3293013674,2839859845&fm=21&gp=0.jpg
1. 没有起点，
2. 没有终点。
3. 三角干吗的，正方形干吗的，没有规范。

一定要流程图的话， 也使用UML的活动图。 http://www.ibm.com/developerworks/cn/rational/tip-drawuml/figure1.gif

# 如何自学

## 1. 英语不能差。 英语对于程序员，就好比鼻子对于大厨。

与国外大牛交流， 要用英文。
看国外官方文档， 都是英文（就连vuejs，作者是中国人， 官方文档都是英文. ruby 日本人写的，官方文档还是英文）
看stackoverflow, 全都是英文。 （stackoverflow 如果是 95分， 国内iteye, infoq 70分， csdn 30 分,给高了。）

## 2. 提高搜索，解决问题的能力。

遇到一个新技术，要有敏锐的嗅觉，知道：这个新技术的
1.官方网站在哪里。
2.源代码地址在哪里。
3.再看这个新技术是否有前景。（一般搜索 “技术A怎么样？", 用英文，在google上搜，就会出来特别有价值的内容)
4.如果有价值学习，那么就来到官网，看tutorial。guide.
5.随着学习的深入，不断的问其他人，不断的参与新项目，要与人交流，所以ruby论坛，线上的要参加，线下的也要参加。
6.多关注一些github。
