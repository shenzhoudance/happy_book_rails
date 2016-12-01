#  如何写接口 (下面有很多在app端的内容)

## 一个例子。

(务必先安装一个json的插件，例如：jsonview)

文档看起来是：

```
展示所有案例列表

前台请求入口：进入home页面后，点击悦案例
后台操作入口： 案例管理 -> 新增案例管理
URL： /interface/cases/all_cases
http://yuehouse.happysoft.cc/interface/cases/all_cases
参数： 无
请求方式：GET

例子：
/interface/cases/all_cases

返回结果：


{

    success: true,
    result: []
}
```


## 如何写接口？

看前面的文档。



## 文档很重要。
把文档写出来（接口文档是必须的，内容有： 名字，用途，例子，必要的说明）

## 写接口的原则

1. 文档务必有，这个文档是给自己看的。
2. 所有的图片，文件，务必用绝对路径。 例如：

```json
{
  result: {

    # 这个是错误的。
    image: "/my_image/1.jpg"

    # 这个是正确的。
    image: "http://image.your_domain.com/my_image/1.jpg"
  }
}
```

3. 所有的http 请求，都要写在一个配置文件中。例如：

我们的站点：  jiayou.com

接口：

我们有三个接口（后台上）：  用户列表，案例列表，监理列表。

-用户列表：   /interface/users
-案例列表：   /interface/cases
-监理列表：   /interface/supervisors

那么，我们在 app中，要怎么写呢？

1. 请求的时候，一般这么写：

```
SETTING = {
  host = 'http://jiayou.com'
}
xhr = Ti.Network.HTTPClient();
...

xhr.open('GET', SETTING.host + "/interface/users")
xhr.send()
```

也就是说，把能提取出来的配置项，都放到配置文件中。例如： 后端站点的域名。


## 接口的另一个原则： 只增不减。

08 年： 写了 10个接口
09年： 写了20个接口
。。。
15年： 再写XX个接口。

那么，08， 09年写的老接口，不要删。

1.0版本的接口，可能到了2.0 就没用了。但是，老接口永远不要删。 因为，你不知道还有多少人在用1.0的版本。

比如： 优酷。 13年的时候，客户端到了3.0版本。但是，对于老版本（1.X的版本），只支持android 2.x的，

全国有3%的人在用。  600万。

## 接口的第三个原则：尽量照顾后续的统计功能，和其他功能。

在访问接口时，把机器的型号，android、ios，GUID，和其他的东东，都作为参数，发送到服务器端。

机器型号： 可以获得的（ phone, pad, tv)

系统版本： 4,0,  4.2  5.0 SDK,  IOS7/8

操作系统： android/ios

pid 渠道id.  （用户获取app的渠道，例如： 360，appstore, 应用宝）

 pid:     360:   001

          appstore:  002

          应用宝：  003

md5    : 为了服务器的安全。   ( timestamp, token , )

详细请看：

2015-01-03 接口的访问基本机制：服务器端的认证 ( web interface basics: server side authentication)
2015-01-03 接口的访问基本机制：User Agent (web interface basic: user-agent)
2015-01-03 接口的访问基本机制：渠道id ( web interface basics: channel id)
2015-01-01 接口的访问基本机制：安全控制-神秘的md5参数（ web interface basic: how to secure your web access? md5, guid, sign )
