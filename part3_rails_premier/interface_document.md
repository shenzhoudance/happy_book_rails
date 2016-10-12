# 接口

注： 接口(interface) 有若干概念：

1. 移动开发时， app 向服务器端请求数据， 服务器端需要有接口。我们讲的就是这个。
2. 编程语言的特性。例如：在java中， 需要有interface. 每个interface都要有implementation.
不仅仅在java, object c 里面，也是一样。

这里说的接口，就是：设计模式中的接口。 我们不讲它。 也不建议使用java/oc 这样的语言。
编程语言中的interface, 虽然比继承( extend, inherit ）功能高级， 但是，不如mix-in好用。

## 概念

是两个不同系统之间， 传递数据的出口 或者 入口。

财务系统，  跟 员工系统，

两个系统之间， 传递数据的出入口， 就是接口。

接口可以在 任意一个系统上。

例如：  财务系统有个接口，  员工系统来读取。  反之亦然。


可以有读数据的接口， 也可以有写数据的接口。

例如：  员工系统， 要显示财务系统的 相关数据：    接口就是只读的。
                   要修改财务系统的 数据， 接口就是 用来写的。


接口跟语言无关。
例如：  字符串，  我可以用java处理，也可以用php, .net, c来处理。

所以字符串 是接口中内容的基础。

## 历史

早期的接口，传递的内容， 都是 XML：

```
<xml>
    <message>你好阿 </message>
</xml>
```
后来大家发现，XML 特别复杂。

```
<xml>
    <student>
      <name>小王</name>
      <sex>Male</sex>
      <age>18</age>
      <birthday>1998-10-10</birthday>
    </student>
</xml>
```

字数多。 层级多。

所以，现在，大家都用json.

## JSON: (另见一章： json 入门）

```
{
  student: {
    name: '小王',
    sex: 'Male',
    age: '18',
    birthday: '1998-10-10'
  }
}

```
体积减少 30%， 而且特别清晰。


## Rails中写接口

## 只读的：


## 写数据库的。

## JSON入门

## 知道接口与 GUI的区别。

接口： 是给设备用的。
GUI (Graphic User Interface)： 是给人用的。

所以，在rails中：  有7个action:
new, edit ... 页面，因为RAISL生成的是 GUI.

对于接口： 是没有必要有 new, edit 这些内容。

## 使用PostMan来进行对接口的测试。
（见工具一章）

========================================================

# 接口文档

通过后台提供的接口，访问服务器端的数据。

## 实现步骤
### 1.统一入口文件
```

    siwei.tech/interface/cases/all_cases

为了更好的保护、维护我们的代码，通常将接口文件写在项目的：./controllers/interface/目录下。
注：在我们的项目中，android/ios访问同一个接口，pc端直接读取后台数据库。
```
### 2.接口的实现
```
class Interface::CasesController < ActionController::Base
  def all_cases
    all_cases = Case.all.map  do |the_case|
    { :id => the_case.id,
      :name => the_case.name,
      :desc => the_case.desc,
      :site => the_case.site,
      :layout_name => the_case.layout_name,
      :total_area => the_case.total_area,
      :package_name => the_case.package_name,
      :cover => SERVER + the_case.cover_url,
      :style => the_case.style,
      :layout => SERVER + the_case.layout.to_s,
    }
  end
    render :json => { :success =>true, :result => all_cases}
  end
end
```
### 3.接口路由的配置
```
namespace :interface do
  resources :init, :only => [] do
    collection do
      get :index
      get :home_sliders
    end
  end

  resources :cases, :only => [] do
    collection do
      get :all_cases
      get :all_styles
      get :select_cases_by_style
      get :select_details_by_id
    end
  end
end
```
### 4.最终接口的调用
```
http://域名/interface/cases/all_cases
```
### 5.返回的结果
```
{
  success: true,
    result: [
      {
        id: 19,
        name: "混搭风，见你所见的情怀",
        desc: "世界那么大，也走过很多东方，领略过地中海长长的海岸线，抚摸过仿佛被水冲刷过的白墙，浅尝过爬藤散发出的阳光芬芳；记忆中亦或是梦里，铺满着家乡的味道。 ",
        site: "北京",
        layout_name: "二室一厅一卫一厨",
        total_area: "110",
        package_name: "老房新装包",
        cover: "http://____2_.jpg",
        style: "混搭",
        layout: "http://__.jpg"
      },
      {
        id: 20,
        name: "时尚是经典的轮回",
        desc: "17世纪爱丽舍宫在巴黎香榭丽舍大街落成，同时期一种思潮古典主义从巴黎席卷欧洲；18世纪许多新材料和工艺的问世，新古典也应运而生， 无论是古典主义还是新古典，都是向古代希腊罗马艺术的高度认同。今天古典主义、新古典依旧散发它特有光环，演绎着当下的时尚。时尚其实就是一场经典的轮回。",
        site: "北京",
        layout_name: "二室一厅二卫一厨",
        total_area: "106",
        package_name: "老房新装包",
        cover: "http://__PS.jpg",
        style: "简欧",
        layout: "http://20/05.jpg"
      }
   ]
}
```



作业

1. 写个 显示 所有的 books的接口
2. 写个 创建(create)/更新(update) 某个 book的接口。
3. 新建一个git repo. 放到github 上去。
