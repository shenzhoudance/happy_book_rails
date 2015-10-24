# 解析XML/HTML： nokogiri  (html parser)

我们使用nokogiri这个rubygem来解析HTML/XML的内容。

nokogiri可以非常方便的让我们解析HTML,XML内容，它支持XPATH 和 CSS selector
这样的方式来找到目标元素(element/node)

## 安装

参考官方网站，对于Ubuntu,需要安装好 libxml2, libxslt 这两个组件：

```bash
$ apt-get install libxml2 libxslt
```

然后就可以：
```bash
$ gem install nokogiri
```

## 解析

可以从文件，字符串，URL等来解析。靠的是这两个方法 `Nokogiri::HTML`, `Nokogiri::XML`：

- 读取字符串：
```ruby
html_doc = Nokogiri::HTML("<html><body><h1>Mr. Belvedere Fan Club</h1></body></html>")
xml_doc  = Nokogiri::XML("<root><aliens><alien><name>Alf</name></alien></aliens></root>")
```

- 读取文件：

```ruby
f = File.open("blossom.xml")
doc = Nokogiri::XML(f)
f.close
```

- 读取URL
```ruby
require 'open-uri'
doc = Nokogiri::HTML(open("http://www.threescompany.com/"))
```

## 寻找节点

可以使用XPATH 以及 CSS selector 来搜索：
例如，给定一个XML：

```xml
<books>
  <book>
    <title>Stars</title>
  </book>
  <book>
    <title>Moon</title>
  </book>
</books>
```

xpath:
```ruby
@doc.xpath("//title")
```

css:
```ruby
@doc.css("book title")
```

## 修改节点内容

```ruby
title = @doc.css("book title").firsto
title.content = 'new title'
puts @doc.to_html

# =>
...
  <title>new title</title>
...
```

## 修改节点的结构

```ruby
first_title = @doc.at_css('title')
second_book = @doc.css('book').last

# 可以把第一个title放到第二个book中
first_title.parent = second_book

# 也可以随意摆放。
second_book.add_next_sibling(first_title)

# 也可以修改对应的class
first_title.name = 'h2'
first_title['class']='red_color'
puts @doc.to_html
# => <h2 class='red_color'>...</h2>

# 也可以新建一个node
third_book = Nokogiri::XML::Node.new 'book', @doc
third_book.content = 'I am the third book'
second_book.add_next_sibling third_book
puts @doc.to_html
# =>
...
<books>
  ...
  <book>I am the third book</book>
</books>
```

还有更多的功能，例如为多个node增加wrapper, 为整个文档增加<?xml.. ?> 这样的字符串等等。
请大家查看API。

## 一个例子, ruby 解析 html页面.

```ruby
require 'httparty'
require 'nokogiri'

url ='https://github.com/rdoc/rdoc/issues/190'
response = HTTParty.get  url
puts response.body
doc = Nokogiri::HTML response.body
doc.css('.js-issue-title').each do  |node|
  puts node.content
end
```

## 注意

当目标HTML文件内容过大时， 试试XPATH会有更好的性能哦

我的一个方法，原来使用了 `.ancestors().first` ,   运行一次3 分钟。

后来改成了 `xpath('./ancestor::table[1]')` , 运行一次 5秒。

节省了大约96%的时间！

```
Finished in 5.81 seconds
3 examples, 0 failures
```
