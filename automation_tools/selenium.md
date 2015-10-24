#Selenium
Selenium是ThoughtWorks公司开发的一套web应用的自动化测试工具，提供测试浏览器的兼容性、测试系统功能。Selenium主要调用浏览器内部API，直接对页面进行操作，并且提供了多种开发语言，可以使用`java`,`c#`,`python`,`ruby`,`php`,`perl`,`javascript`等语言来写测试脚本，具有很高的灵活性。

Selenium主要有`Selenium WebDriver`,`Selenium RC`,`Selenium IDE`,`Selenium-Grid`四大类工具。
* Selenium WebDriver：也称为Selenium 2，提供一系列面向对象的API和解决旧方法的限制的解决方案。其直接在浏览器中进行测试。
* Selenium RC：也称为Selenium 1，WebDriver出现之前的主要测试方法，现今已被弃用。
* Selenium IDE：一个测试脚本的集成环境，是Firefox的一个插件，提供简单易用的操作界面。具有录制功能，能够录制用户操作过程，并导出成脚本，随后可以直接执行该脚本进行测试。
* Selenium-Grid：允许Selenium RC解决方案处理大型测试套件，能够运行在不同的环境中。并且可以同时在不同的远程机器上运行不同的测试。

Selenium IDE使用方法[教程](http://www.seleniumhq.org/docs/02_selenium_ide.jsp),Selenium比较简单，可以直接查看教程。

由于Selenium WebDriver直接在浏览器中进行测试，且提供了多种语言的面向对象API，所以开发非常具有灵活性，本节主要介绍使用Selenium WebDriver。
Selenium WebDriver支持的浏览器有`ie`,`internet_explorer`,`edge`,`remote`,`chrome`,`firefox`,`ff`,`android`,`iphone`,`phantomjs`,`safar`。本次使用chrome浏览器进行测试。

ruby提供了Selenium的gem，所以安装方便
```bash
$ gem install Selenium
```
当使用chrome测试时，需要安装[ChromeDrive](https://sites.google.com/a/chromium.org/chromedriver/),并把下载的可执行文件放到`$PATH`中，可以在终端直接访问。

一个打开百度，并输入搜索今天天气的例子：`test.rb`，该过程使用了rails的rspec单元测试，让程序以单元测试的方式运行，并能够详细的知道运行细节。
```ruby
#encoding : utf-8
require 'rubygems'
require 'selenium-webdriver'

chrome = Selenium::WebDriver.for :chrome

describe "test baidu search" do

  it "open baidu.com in chrome and search with a key word" do
    url = "http://www.baidu.com"
    chrome.get url
    sleep 1
    search_box = chrome.find_element(:id , "kw")
    puts search_box
    search_box.send_keys("今天天气")
    chrome.find_element(:id,"su").click

    sleep 3
    chrome.quit
  end
end
```
运行
```bash
$ rspec test.rb
$ rspec test.rb -fh > result.html #输出结果到result.html
```

#####获取网页
```ruby
chrome.get "http://www.baidu.com"
```
`chrome.get`方法用于获取一个网页，selenium可能会等待网页加载完再执行下一步操作，有可能不，某些时候我们需要指定一个特定的等待。

#####查找元素
```ruby
chrome.find_element(:id, "kw")
```
WebDriver提供在当前页面查找元素的方法`find_element`,并且有多重查找方法,id,class,tag,name,link,css,xpath等。

通过id查找：
```html
<input id='kw' />
```
```ruby
driver.find_element(:id,'kw')
```

通过class名称查找：
```html
<div class="cheese"><span>Cheddar</span></div>
<div class="cheese"><span>Gouda</span></div>
```
```ruby
driver.find_element(:class_name,'cheese')
#或者
driver.find_element(:class,'cheese')
```

`find_element`提供的查找方法：`:class, :class_name, :css, :id, :link_text, :link, :partial_link_text, :name, :tag_name, :xpath`，其中根据xpath查找元素，可能会根据浏览器支持的不同而不同。

```html
<input type="text" name="example" />
<INPUT type="text" name="other" />
```
```ruby
input = driver.find_element(:xpath,"//input") ##可能会找到`input`一个，可能找到`input`和`INPUT`两个
```

#####输入值
```html
<input type="text" id="INP"/>
```
```ruby
input = driver.find_element(:id,"INP")
input.send_keys("weather")
```
send_keys为给找到的元素填入字符，通常为输入框等可以输入的元素，否则不接受。

Selenium提供很多API，具体参考[selenium webdriver](http://www.seleniumhq.org/docs/03_webdriver.jsp), [selenium api](http://seleniumhq.github.io/selenium/docs/api/rb/frames.html#!_index.html)

