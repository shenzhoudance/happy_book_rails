# 接口文档
通过后台提供的接口，访问服务器端的数据。

## 实现步骤
### 1.统一入口文件
```
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
