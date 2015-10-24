# 所见即所得编辑器  WYSIWYG editor

ckeditor 是目前最流行的所见即所得编辑器。它可以为用户提供极大地方便。

注意： 不要使用 "ckeditor_rails" 这个gem.

![ckeditor例子](/images/ckeditor.png)

1。 安装 imagemagick

Linux下：

```bash
$ sudo apt-get install imagemagick
```

2. 编辑： Gemfile

```ruby
gem 'carrierwave'
gem 'mini_magick'
gem 'ckeditor'
```

3. 在使用rails的前提下：

```bash
$ rails generate ckeditor:install --orm=active_record --backend=carrierwave
```

4. 在application.js 或者 application.js.erb文件中：

```
//= require ckeditor/init
```

5.
```
$ rake db:migrate
```

6.

```ruby
<%= form_for ... do |f| %>
  <%= f.cktext_area :introduction %>
<% end %>
```

注意, 做完上面几步, ckeditor是出来了,但是还没有 "上传文件" 这个功能. 所以还需要解决这个问题,参考(
http://stackoverflow.com/questions/20010675/no-image-upload-capability-in-ui-with-ckeditor-gem-paperclip-rails-4 )

7.  add this file to : app/assets/javascripts/ckeditor/config.js

```javascript
/*
Copyright (c) 2003-2011, CKSource - Frederico Knabben. All rights reserved.
For licensing, see LICENSE.html or http://ckeditor.com/license
*/

CKEDITOR.editorConfig = function( config )
{
  // Define changes to default configuration here. For example:
  // config.language = 'zh-CN';
  config.height='600px';
  // config.uiColor = '#AADC6E';

  /* Filebrowser routes */
  // The location of an external file browser, that should be launched when "Browse Server" button is pressed.
  config.filebrowserBrowseUrl = "/ckeditor/attachment_files";

  // The location of an external file browser, that should be launched when "Browse Server" button is pressed in the Flash dialog.
  config.filebrowserFlashBrowseUrl = "/ckeditor/attachment_files";

  // The location of a script that handles file uploads in the Flash dialog.
  config.filebrowserFlashUploadUrl = "/ckeditor/attachment_files";

  // The location of an external file browser, that should be launched when "Browse Server" button is pressed in the Link tab of Image dialog.
  config.filebrowserImageBrowseLinkUrl = "/ckeditor/pictures";

  // The location of an external file browser, that should be launched when "Browse Server" button is pressed in the Image dialog.
  config.filebrowserImageBrowseUrl = "/ckeditor/pictures";

  // The location of a script that handles file uploads in the Image dialog.
  config.filebrowserImageUploadUrl = "/ckeditor/pictures";

  // The location of a script that handles file uploads.
  config.filebrowserUploadUrl = "/ckeditor/attachment_files";

  // Rails CSRF token
  config.filebrowserParams = function(){
    var csrf_token, csrf_param, meta,
        metas = document.getElementsByTagName('meta'),
        params = new Object();

    for ( var i = 0 ; i < metas.length ; i++ ){
      meta = metas[i];

      switch(meta.name) {
        case "csrf-token":
          csrf_token = meta.content;
          break;
        case "csrf-param":
          csrf_param = meta.content;
          break;
        default:
          continue;
      }
    }

    if (csrf_param !== undefined && csrf_token !== undefined) {
      params[csrf_param] = csrf_token;
    }

    return params;
  };

  config.addQueryString = function( url, params ){
    var queryString = [];

    if ( !params ) {
      return url;
    } else {
      for ( var i in params )
        queryString.push( i + "=" + encodeURIComponent( params[ i ] ) );
    }

    return url + ( ( url.indexOf( "?" ) != -1 ) ? "&" : "?" ) + queryString.join( "&" );
  };

  // Integrate Rails CSRF token into file upload dialogs (link, image, attachment and flash)
  CKEDITOR.on( 'dialogDefinition', function( ev ){
    // Take the dialog name and its definition from the event data.
    var dialogName = ev.data.name;
    var dialogDefinition = ev.data.definition;
    var content, upload;

    if (CKEDITOR.tools.indexOf(['link', 'image', 'attachment', 'flash'], dialogName) > -1) {
      content = (dialogDefinition.getContents('Upload') || dialogDefinition.getContents('upload'));
      upload = (content == null ? null : content.get('upload'));

      if (upload && upload.filebrowser && upload.filebrowser['params'] === undefined) {
        upload.filebrowser['params'] = config.filebrowserParams();
        upload.action = config.addQueryString(upload.action, upload.filebrowser['params']);
      }
    }
  });
};
```

8. 默认的编辑页面的高度是200px, 需要我们做一些定制，例如增加高度，使用汉语：

```
$ mkdir app/assets/javascripts/ckeditor/ -p
```

在上面的目录下，新增config.js:
```javascript
CKEDITOR.editorConfig = function( config ) {
  // Define changes to default configuration here. For example:
  // config.language = 'fr';
  // config.uiColor = '#AADC6E';
  config.language = 'zh-CN';
  config.height='600px'
};
```

## 注意1: ckeditor的语言包特别大。

在rails中使用它时，务必指定它的语言，并且编译 assets,
否则每次需要加载几十个文件。


## 注意2：ckeditor中，只能输入inline style,

之前写过  ckeditor 会默认吃掉inline style (http://siwei.me/blog/posts/ckeditor-inline-style-preserve-inline-style-in-ckeditor)

其实, ckeditor, 还会吃掉:  <script> ,  <style>,  以及所有的 '<' 标签. (把它转换成:  &lt; )

为什么呢?  这是考虑到 安全性.

设想一个场景:  某个 CSDN 的 BLOG 用户,发布了一个博客文章, 里面嵌入了一段js hacker的代码:

<script >  some_dirty_code ... </script>

那么就会对所有访问这个页面的人造成损失.

所以, CKEDITOR , 以及其他好多编辑器,都是默认输入的内容是有限制性的.

或者，使用：

```javascript
// in your ckeditor's config.js
config.allowedContent = true;
```

