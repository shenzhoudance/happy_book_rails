# 使用grunt进行自动化 

refer to:  http://gruntjs.com/getting-started

## 简介

自动化永远是我们程序员所追求的方式. 

对于c 有 make ,对于 ruby 有 rake, 对于 java 有ant, maven, ivry, 对于js 则是 grunt.

Rails 或者 Titanium 自带的一些命令虽然好用, ( $ alloy compile,  $ ti build/clean) 
但是粒度略小. 比如我要用jade 编写xml, 用coffee编写js , 就需要在 运行 
`$ alloy compile` 之前先运行 一个 脚本: 


```bash
# file name:  compile_my_coffee_to_js: 
#compile coffee
cd app/controllers/
coffee -bc *.coffee
cd ../../

cd app/controllers/home/
cat home_market.coffee home_master.coffee home_me.coffee home_competition.coffee home_community.coffee home_version.coffee home_frame.coffee | coffee -bc --stdio  > ../home.js
cd ../../../
```

其实每次运行这个脚本就很烦. 而且可读性差. 而且跟grunt语言相比有很大的缺点
(最突出的就是不如grunt专业和全面), 将来维护的话必然是个大坑. 
( 想起之前有运维同学专门手写了个 shell 部署脚本的程序, 花了好几天时间,结果写出来的
东西 跟capistrano 相差甚远, 不但可读性差(shell v.s. ruby, 呵呵) 而且无法回滚.

所以 , 有好轮子就要用. 对自身也是个提高. 

再之, 考虑到 grunt 本身就是个国宝级项目 (github 9133 个关注), 我们是务必要用的.

## 安装:  

```bash
$ npm install -g grunt
$ npm install -g grunt-cli
```

## 运行: 

需要本地目录有 Gruntfile ( 跟Makefile, Rakefile 极度类似) , package.json (跟 Gemfile 极度类似) 

`Gruntfile.(js|cofee)`:  定义了要运行的各种方法 . 下面是个例子:

```javascript
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // Default task(s).
  grunt.registerTask('default', ['uglify']);
};
```

package.json: 跟Gemfile一样, 定义了各种依赖包. 通过 `$ grunt-init` 或者 `$npm init ` 来生成.下面是个例子: 

```javascript
{
  "name": "my-project-name",
  "version": "0.1.0",
  "devDependencies": {
    "grunt": "~0.4.5",
    "grunt-contrib-jshint": "~0.10.0",
    "grunt-contrib-nodeunit": "~0.4.1",
    "grunt-contrib-uglify": "~0.5.0"
  }
}
```

在 Gruntfile 中,可以使用 `<% %>` 来引用配置文件的各种信息:

```javascript
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  uglify: {
    options: {
      banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
    },
    build: {
      src: 'src/<%= pkg.name %>.js',
      dest: 'build/<%= pkg.name %>.min.js'
    }
  }
});
```

## 加载任务( load task) :

```javascript
  // 加载自定义任务，　并且默认运行：
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认提供了　plugin: 
  grunt.registerTask('default', ['uglify']);  

  // 如果没有提供plugin的话，就直接　写出来：
  grunt.registerTask('default', 'Log some stuff.', function() {
    grunt.log.write('Logging some stuff...').ok();
  });
```

## 使用 grunt时,生成多个文件的参数: (options grunt used generate files )

很多时候, 我们会运行一条命令生成多个文件, 例如: `coffee *.coffee`

在grunt中, 运行类似的命令时,就要使用 对应的参数了. 

首先要把 `expand: true` 设置好. 

* cwd:  被处理文件(src中定义的文件）的相对路径 
* src: 被处理的文件的格式.  
* dest: 生成的文件的路径前缀. 
* ext: 在生成的文件中, 使用这个扩展名.  
* flatten: 把生成的文件名中的路径部分去掉. 只留下纯文件名.  

下面是一个 例子:  (注意 files: 后面对应的是一个 array [] , 而不是一个 hash: {} )

```coffeescript
# Gruntfile.coffee 
module.exports = (grunt) ->
  grunt.initConfig
    coffee:
      compile_multiple_files:
        options:
          bare: true

        # 编译多个文件, 记得files 后面永远是个数组
        files:
          [
            expand: true
            flatten: true
            cwd: 'app/controllers/'
            src: ['*.coffee']
            dest: 'app/controllers/'
            ext: '.js'
          ]

  #grunt.loadNpmTasks 'grunt-contrib-watch'
  grunt.loadNpmTasks 'grunt-contrib-coffee'

  grunt.registerTask 'default', [ 'coffee' ]
```

如果遇到 `Object true has no method 'indexOf'` , 就一定是 files 后面跟的不是 array. 

## 使用 grunt 来编译 jade, stss, coffee


## grunt 管理仅发生变化的文件: grunt-watch + grunt-newer

refer to: https://github.com/tschaub/grunt-newer

grunt-newer: 管理仅发生变化的文件

grunt-watch 能监控文件的变动. 它俩要配合使用。

### 安装：

1. 编辑 package.json ,

```json
// package.json; 
'devDependencies': {
    "grunt-newer": "1.1.0"
    "grunt-contrib-watch": "~0.5.1",
}
```

```bash
$ npm install 
```

2. 然后，Gruntfile.coffee中，加上 watch 任务：

```coffeescript
module.exports = (grunt) ->
  grunt.initConfig

    # 这个最重要。
    watch:
      options:
        nospawn: true
      views:
        files: ['src/**/*.jade']
        tasks: ['newer:jade']
      styles:
        files: ['src/**/*.stss']
        tasks: ['newer:stss']
      javascripts:
        files: ['src/**/*.coffee']
        tasks: ['newer:coffee']

    coffee:
      compile_multiple_files:
        options:
          bare: true
        files:
          # 1:1 的编译文件
          #'app/controllers/simulate_stocks.js': 'app/controllers/simulate_stocks.coffee'
          # 编译多个文件

          [
            expand: true
            flatten: true
            cwd: 'src/controllers/'
            src: ['*.coffee']
            dest: 'app/controllers/'
            ext: '.js'
          ]

      compile_flat_details:
        options:
          bare: true
        files:
          # 把多个文件合并
          'app/controllers/flatDetails.js': [
            'src/controllers/flat_details/1*.coffee'
            'src/controllers/flat_details/2*.coffee'
            'src/controllers/flat_details/3*.coffee'
          ]

      compile_diy_project:
        options:
          bare: true
        files:
          # 把多个文件合并
          'app/controllers/diyProject.js': [
            'src/controllers/diy_project/1*.coffee'
            'src/controllers/diy_project/2*.coffee'
            'src/controllers/diy_project/3*.coffee'
            'src/controllers/diy_project/4*.coffee'
            'src/controllers/diy_project/5*.coffee'
          ]

    jade:
      compile:
        options:
          pretty: true
        files: [
          expand: true
          src: ['views/*.jade']
          dest: 'app' # 写成  'app/views' 也行,
          cwd: 'src'  # 如果 dest是'app/views', 这里就要是 'src/views'
          ext: '.xml'
        ]

    stss:
      compile:
        files:[
          expand: true
          src: ['**/*.stss']
          dest: 'app/styles'
          cwd: 'src/styles'
          ext: '.tss'
        ]

  grunt.loadNpmTasks 'grunt-contrib-watch'
  grunt.loadNpmTasks 'grunt-newer'
  grunt.loadNpmTasks 'grunt-contrib-coffee'
  grunt.loadNpmTasks 'grunt-contrib-jade'
  grunt.loadNpmTasks 'grunt-stss'

  grunt.registerTask 'default', [ 'coffee', 'jade','stss' ]
```

3. 运行： 

```
$ grunt watch 
```

这样，你的每次改动，都会瞬间被监控到，然后调用对应的命令。例如，每改一个jade 文件，watch就会调用 jade 这个任务。

## 在grunt中使用jade 的例子

```coffeescript
# Gruntfile.coffee
module.exports = (grunt) ->
  grunt.initConfig
    jade:
      compile:
        options:
          pretty: true
        files: [
          expand: true
          src: ['**/*.jade']
          dest: 'app' # 写成  'app/views' 也行,
          cwd: 'src'  # 如果 dest是'app/views', 这里就要是 'src/views'
          ext: '.xml'
        ]

  grunt.loadNpmTasks 'grunt-contrib-jade'
  grunt.registerTask 'default', [ 'jade' ]
```

3. 编译: $ grunt 

### grunt 编译 coffeescript

npm install:  grunt  , grunt-cli, grunt-contrib-coffee

```bash
$ npm install grunt-contrib-coffee --save-dev 
```

编辑:  Gruntfile.coffee: 

```coffeescript
module.exports = (grunt) ->
  grunt.initConfig
    coffee:
      compile_multiple_files:
        options:
          bare: true
        files:
          # 1:1 的编译文件
          #'app/controllers/simulate_stocks.js': 'app/controllers/simulate_stocks.coffee'
          # 编译多个文件
          [
            expand: true
            flatten: true
            cwd: 'app/controllers/'
            src: ['*.coffee']
            dest: 'app/controllers/'
            ext: '.js'
          ]
      compile_home_js:
        options:
          bare: true
        files:
          # 把多个文件合并
          'app/controllers/home.js': [
            'app/controllers/home/home_market.coffee'
            'app/controllers/home/home_master.coffee'
            'app/controllers/home/home_me.coffee'
            'app/controllers/home/home_competition.coffee'
            'app/controllers/home/home_community.coffee'
            'app/controllers/home/home_version.coffee'
            'app/controllers/home/home_frame.coffee'
          ]
  #grunt.loadNpmTasks 'grunt-contrib-watch'
  grunt.loadNpmTasks 'grunt-contrib-coffee'

  grunt.registerTask 'default', [ 'coffee' ]
```

然后运行: `$ grunt`

就会把 coffee的所有 子任务 (sub- tasks) 都依次运行了. 结果如下; 

```bash
$ grunt
Running "coffee:compile_multiple_files" (coffee) task
>> 53 files created.

Running "coffee:compile_home_js" (coffee) task
>> 1 files created.

Done, without errors.
```

