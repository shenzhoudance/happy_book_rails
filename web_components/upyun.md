# 图片云存储 upyun

通常的web项目都需要图片上传功能．但是保存在本地的话(例如carrierwave)，转移，
管理都非常不方便．

而且还需要设置各种访问路径．操作起来也可以忍受，但总感觉不是那么利落．

所以云存储用来保存图片非常合适．几个最好的结果：

１．三重备份
２．有独立的ＵＲＬ，　再也不担心　相对路径的问题了！
３．可以使用比较好的ＣＤＮ加速．服务器在香港返回一段html body, 而该body内容中的image　就可以在大陆内访问．速度极快

同时，upyun可以保存的不仅仅是图片，还可以是mp3等文件。

## 注册

注册很简单。不说了。
需要注意的是： 你注册完之后会得到bucket, operator, password

- bucket: 子空间。 你可以有多个bucket
- operator: 操作员id. 对于同一个bucket, 我们可以允许多个人操作
- password: 上面的操作员的id. 没有密码它做不了什么。

有了上面三个信息，就可以上传图片了。

## 绑定域名

![例子](/images/upyun_bind_domain.png)

## 最简单的例子
rails upyun上传图片的例子

核心是使用了rubygem:  upyun,  另外，记得要绑定好　二级域名（image.happysoft.cc)

```ruby
  require 'upyun'
  def create
    @upload = Upload.new(upload_params)
    @upload.save

    bucket = 'generic'
    operator = 'operator_id'
    password = 'your password'
    upload_file = params[:file]
    upyun = Upyun::Rest.new(bucket, operator, password)
    remote_file = "/image/#{@upload.id}/#{upload_file.original_filename}"
    response = upyun.put remote_file, upload_file.read
    render :json => {
      :response => response,
      :url => "http://image.happysoft.cc#{remote_file}"
    }
  end
```


## 把已有项目的本地已经存在的文件上传到upyun

把下面代码COPY到某个RB文件中并执行。

前提是你有了  bucket， operator,  operator password..

```ruby
# copy_all_files_to_upyun.rb
require 'upyun'
upyun = Upyun::Rest.new('your_bucket', 'operator_name', 'password')
# 本地路径：  ./folder/1/2/3.jpg, 那么，上传到 远程后，应该是：  /1/2/3.jpg, happyteam.b0.upaiyun.com/1/2/3.jpg
# 执行这个脚本的要求是：
# 1. cd 到 目标文件夹的同级目录，例如：  /opt/app/yuehouse_web/public
# 2. 目标文件夹（例如： files ). 直接：
root ='/opt/app/yuehouse/shared/public'
folder = '/uploads'
files_and_folders = `find #{root + folder}`

files_and_folders.split("\n").select{ |entity|
  entity.include?(".")
}.each { |file|
  remote_file_path = file.sub(root, '')
  puts "=remote_file_path: #{remote_file_path.inspect}"
  puts "= local file: #{file.inspect}"
  puts upyun.put(remote_file_path, File.new(file))
}
```

## 与carrier wave 同时使用
参考：https://github.com/nowa/carrierwave-upyun

1. 在Gemfile中：
```ruby
gem 'carrierwave'
gem 'carrierwave-upyun'
```

2. 在config/initializers/carrierwave.rb中，增加：
```ruby
CarrierWave.configure do |config|
  config.storage = :upyun
  config.upyun_username = "xxxxxx"
  config.upyun_password = 'xxxxxx'
  config.upyun_bucket = "my_bucket"
  config.upyun_bucket_host = "http://my_bucket.files.example.com"
end
```

3. 在对应的uploader中, 使用 `storage :upyun`：

```ruby
class AvatarUploader < CarrierWave::Uploader::Base
  storage :upyun
end
```

## 把你的二级域名绑定到upyun上。

默认情况下，你的upyun上的图片的访问地址是：

`http://yuehouse.b0.upaiyun.com/hello.jpg`

非常难看对不对。我们可以把我们的二级域名绑定上去。具体请自行查询文档。

![二级域名绑定到upyun上](/images/upyun_bind_domain.png)


## 使用图片的缩略图

图片的缩略图特别重要。 比如，在列表页中应该显示缩略图，在详细页中应该显示大图。
我们不应该关心这些事情，应该把它交给云端去做。

在upyun中有两种空间： 一种是图片空间，一种是文件空间。只有前者支持缩略图。

步骤：
1. 登陆upyun
2. 选择目标图片空间

3. 选择 自定义版本

![例子](/images/upyun_menu.png)

4. 创建 缩略图版本即可。

![例子](/images/upyun_create_thumbneil.png)

一个比较好的实践，如图所示，直接把缩略图的名字以 aaxbb命名，例如图中的'50x50',
访问的时候，
```
原图：   http://yourdomain.com/hello.jpg
缩略图： http://yourdomain.com/hello.jpg!50x50
```
