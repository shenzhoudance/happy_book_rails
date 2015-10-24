# Rails处理图片上传: CarrierWave

文件上传可以说处处都有，我们在论坛中上传附件，在注册一个新用户时上传头像，
编辑一个文章时要上传图片等等。

CarrierWave(https://github.com/carrierwaveuploader/carrierwave)
用来把客户端上传的图片保存到服务器本地。它支持包括Rails, Sinatra 在内的多种框架。

注意: 以下仅支持 carrierwave  0.9.0 版本. 0.10.0 与 1.0 版本的用法略有变化, 请注意查看官方文档

1.0 版本仅支持 ruby 2.0 与 rails 4 .

## 使用
```
in Gemfile:
gem 'carrierwave', '0.9.0'
```

```
$ rails generate uploader Logo
```
会新建一个这样的model:
```ruby
# this will create file: app/uploaders/logo_uploader.rb
class AvatarUploader < Logo::Uploader::Base
  storage :file
end
````

同时, 务必记得 为对应的model 增加 列:  Avatar

```ruby
# -*- encoding : utf-8 -*-
class AddSnapshotToMarketModules < ActiveRecord::Migration
  def change
    add_column :market_modules, :avatar, :string, default: '', comment: '保存截图路径'
  end
end
```

in your model:

```ruby
class Item < ActiveRecord::Base
  mount_uploader :logo, LogoUploader
end
```

in your view:

```ruby
<%= f.file_field :logo %>
```

in controller :

```ruby
# do nothing....
item.create(params[:item])
# or
item.logo = params[:file]
item.save
```

# 对于 rails4, 只需要把 avatar 这个属性加入到 参数白名单列表中:

```ruby
def item_params
    params.require(:item).permit(:market_id, :name, :level, :avatar)
end
```

用法：
```erb
<%= image_tag @item.logo.url, :style => 'height: 200px' %>
```


