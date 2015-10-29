#Rails接口入门
使用Rails开发后台的时候，很多时候都需要给前端或者什么别的地方提供一些数据接口，这里我们就来讲讲如何入门Rails接口。
## 目录结构
一般之下，我们习惯把我们的controllers目录之下，如下：

+ app

  + controllers

    + interface

      + your_interface_controllers.rb

其实.rb文件的命名也是有规则的,以book为例。我们要写一个book的接口文件来暴露我们准备好的数据，所以按照rails命名规则，文件取名叫做books_controller.rb。
我们可以看看这个接口文件的结构内容：
```ruby
#books_controller.rb
class Interface::CasesController < ActionController::Base
#声明一个继承自ActionController::Base的类
  def all_books
  # 定义一个action来返回所有的book数据中的id,name字段
    all_books = Book.all.map do |book|
      {
        :id => book.id ,
        :name => book.name ,
      }
    end
    render :json =>{:result => all_books}
  end
end
```
以上就完成一个最简单的接口文件，其中定义一个接口叫all_books，然后我们要在routes.rb中配置好路由。打开routes.rb，插入如下代码：
```ruby
#在routes.rb中插入如下代码
namespace :interface do
  resource :book ,:only => [] do
    collection do
      get :all_books
    end
  end
end
```
这样就在路由文件中配置好了all_books的路由。

然后我们在浏览器中输入如下url就可以访问接口了，假设你用的是本地localhost:3000,则如下
http//:localhost:3000/interface/books/all_books


# TODO 下面就是我自己写的草稿
#  如何写接口



## 返回值



1. XML

2. JSON

3. HTML



目前，提到接口，返回的类型，都是json 字符串。



## 在rails里面，只要一个action返回 json，那么我们就可以管这个action成为 接口。



## 一个例子。

(务必先安装一个json的插件，例如：jsonview)



文档看起来是：



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





```json

{

success: true,

           result: [

           {

id: 19,

    name: "混搭风，见你所见的情怀",

    desc: "世界那么大，也走过很多东方，领略过地中海长长的海岸线，抚摸过仿佛被水冲刷过的白墙，浅尝过爬藤散发出的阳光芬芳；记忆中亦或是梦里，铺满着家乡的味道。 ",

    site: "北京朝阳",

    layout_name: "二室一厅一卫一厨",

    total_area: "110",

    package_name: "老房新装包",

    cover: {

cover: {

url: "/uploads/case/cover/19/____2_.jpg"

       }

    },

style: "混搭",

       layout: {

layout: {

url: "/uploads/case/layout/19/__.jpg"

        }

       }

           },

           {

id: 20,

    name: "时尚是经典的轮回",

    desc: "17世纪爱丽舍宫在巴黎香榭丽舍大街落成，同时期一种思潮古典主义从巴黎席卷欧洲；18世纪许多新材料和工艺的问世，新古典也应运而生， 无论是古典主义还是新古典，都是向古代希腊罗马艺术的高度认同。今天古典主义、新古典依旧散发它特有光环，演绎着当下的时尚。时尚其实就是一场经典的轮回。",

    site: "北京丰台区",

    layout_name: "二室一厅二卫一厨",

    total_area: "106",

    package_name: "老房新装包",

    cover: {

cover: {

url: "/uploads/case/cover/20/__PS.jpg"

       }

    },

style: "简欧",

       layout: {

layout: {

url: "/uploads/case/layout/20/05.jpg"

        }

       }

           },

           {

id: 21,

    name: "恋上地中海岸的夏日转角",

    desc: "夏日慵懒的午后，看妩媚的阳光布满房间的每个角落，听着风在耳边轻轻地吟唱，让人不由得心旌摇曳。随意地漫步在地中海绝美的风景线，找一个宁静的去处，静静地闭目养神，任美妙的生活像清泉一般缓慢地流淌，流淌在如歌的岁月之中。这一刻抛却尘世的纷扰，不羁的心也会找到属于它的家园。",

    site: "北京东城区",

    layout_name: "二室一厅二卫一厨",

    total_area: "125",

    package_name: "整装包",

    cover: {

cover: {

url: "/uploads/case/cover/21/C1__1_.jpg"

       }

    },

style: "地中海",

       layout: {

layout: {

url: "/uploads/case/layout/21/_______.jpg"

        }

       }

           },

           {

id: 22,

    name: "简约的极致之美",

    desc: "简约不是简单，而是简洁婉约，独具风致和格调。其品位之高，正如素面素心天生丽质的自然之美一样怡人快心。它的外表是删繁就简，去除造作伪饰，大方淡雅，仪态天成；它的内在是返璞归真，修不动心，清晰稳妥，从容安适，以静制动，平淡为真。",

    site: "北京海淀区",

    layout_name: "二室一厅一卫一厨",

    total_area: "96",

    package_name: "整装包",

    cover: {

cover: {

url: "/uploads/case/cover/22/301__D4______2_.jpg"

       }

    },

style: "简约",

       layout: {

layout: {

url: "/uploads/case/layout/22/301__D4_______.jpg"

        }

       }

           }

         ]

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

用户列表：   /interface/users

案例列表：   /interface/cases

监理列表：   /interface/supervisors





那么，我们在 app中，要怎么写呢？



1. 请求的时候，一般这么写：



SETTING = {

  host = 'http://jiayou.com'

}

xhr = Ti.Network.HTTPClient();

...

xhr.open('GET', SETTING.host + "/interface/users")

xhr.send()





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
