# appium
Appium是一个开源的，用于对iOS和Android上native，hybrid,mobile web应用进行自动化测试的框架。所谓native apps就是通过iOS或Android SDK写出的手机应用。Mobile web Apps就是通过手机浏览器来访问的web apps（Appium支持iOS的Safari浏览器和Android中的Chrome浏览器或者程序中内建浏览器的应用）。Hybrid apps就是拥有一个外壳的webview 应用，通过原生的control来与一个web里的内容进行交互，比如：Phonegap。

##appium的原理
+ 你不需要重新编译或者修改你的app来测试它。

+ 你不用被限定使用某种特定的语言或框架来写你的测试文件。

+ 一个手机测试框架在调用自动化测试的APIs的时候，不应该重复造轮子。

+ 一个手机测试框架应该是开源的。

##appium的好处

+ 这是一个开源的测试框架。

+ 这是一个跨平台的测试框架，你可以测试iOS和Android，以及mobile web 应用。

+ 你可以使用你的喜欢的语言来写你的测试文件，ruby，java，python，javascript，php，c#都可以。

##appium客户端

###appium app
对于appium，我们可以用两个种办法，一个是appium的客户端，在window或者mac os下，我们可以下载appium的客户端。地址是https://bitbucket.org/appium/appium.app/downloads/。

下载下来之后，点击安装好之后，你会看见appium app的用户界面。以Mac为例，appium GUI的官方文档的中有很详细的介绍。地址是https://github.com/appium/appium-dot-app。

我们对其中的一部分内容进行解释：

+ Android按钮：显示安卓设置

+ iOS按钮：显示iOS设置

+ Settings按钮：显示一些通用设置

+ Developer按钮：显示一些开发者设置

+ Robot按钮：显示Robot设置

+ Inspector按钮：打开Inspector界面

+ Doctor按钮：执行appium doctor，对环境进行检测

+ Launche/Stop按钮：启动或停止执行自动化测试的server，即开始测试

+ Clear按钮：清除显示窗中的输出日志

Android设置中一些要注意的事：

+ app path是指你存放你要测试的apk文件的路径。

+ Launch AVD是指你要测试的使用的安卓虚拟设备名称

+ 高级选项中Android SDK Path是指Android SDK的路径，这个很重要


iOS设置中一些要注意的事：

+ app path是指iOS应用的路径，即.app ，.zip，.ipa文件路径。

+ Force Device是指定运行的虚拟设备，这里一定要选择和你开发时xcode所对应的simulator版本。

+ UDID是在你要用真机测试时，所要填写的，如果你选择中了UDID的复选框，你就必须把你开发应用的BundleID填上。


Preference设置中：

+ 默认server address是0.0.0.0，端口可以自己随意设置。

+ 建议勾选Prelaunch Application选项。

###Inspector
配置好上面这些之后，可以点击Inspector按钮，就会打开Inspector 窗口，这里面你会看到一个窗口同步于你的simulator,你可以选择你要的操作方式例如touch,swipe来触发你的应用。

假设你点击了一个按钮，你会在Details窗口里看到这个按钮的很多信息，例如：name，type，value，XPath等等，你可以在编写测试代码的时候，利用上这些重要的信息。

如果你点击record按钮，appium会根据你的操作生成对应的代码，而代码的语言你也可以自由选择，可以选择你喜欢的ruby,或者java等等语言。而对应的代码也是可以导出来的，导出来的测试代码可以通过在命令行执行。


##appium 全局安装，命令行操作
如果你要使用javascript语言来写测试文件的话，首先要安装node，$ brew install node

然后再安装appium，$ npm install -g appium，值得一提的事，由于镜像是在国外，所以国内在没有翻墙的情况下很难安装成功，所以可以使用国内的镜像：$ npm --registry http://registry.cnpmjs.org install -g appium

检测安装的环境是否合格：$ appium-doctor，会输出很多环境信息。

再安装appium client，$ npm install wd

启动appium server ：$ appium &

执行你的测试文件：$ node your_appium_test_file.js

个人偏好用ruby语言来写测试文件，举个例子：find_element(:name,'点击').click()这句话就是通过找到名字叫’点击’的元素，并让它执行点击操作。这里的name键值对可以换成别的，比如ID，XPath等等。


