# Rails Controller 简介

1. Controller 是什么？
2. Routes 是什么？
3. Controller 与 Routes 基本 RESTful 请求的使用方式
4. RESTful 之外的请求如何添加。

## Controller 是什么？

Controller 全称是 Action Controller，是 MVC 中的"C"。

路由将请求转发给 Controller 中对应的 Action，而后由 Controller 中的各个 Action 对相应请求进行处理并作出反馈。

这些 Action 处理请求中的参数，或将其储存至 Model 中，或根据不同的请求和参数组织 Model 中的数据，提供给 View 显示给用户。

## Routes 是什么？

上面说到，路由将会把请求转发给 Controller 中对应的 Action。

另一方面，我们在 View 中使用路由 Helper 辅助方法来生成路径和 URL，这样就能保证我们的 URL 一定有对应的 Action，避免 NoMethodError 找不到对应方法的错误。

## RESTful Routes 与 Controller Action 的对应关系

初学 Rails，大家都会告诉你：Rails 是一个强调“习惯优先配置”的框架。这里会谈到的 RESTful 设计风格就是这一点的重要体现。

如果你参考过一些现有的 Rails 项目，你就会在 routes.rb 文件中看到很多类似下面的代码：

```rails
Rails.application.routes.draw do
  resources :products
end
```

然后 Rails 会按照 RESTful 对资源的定义，自动生成对一个资源(products)最基础的 CRUD（即增删改查）操作对应的路由。

在终端中运行命令：

```bash
$ bundle exec rake routes
```

这个命令会打印出当前项目所有路由。


|      Prefix|Verb  |URI Pattern       |Helper                      |Controller#Action|
|------------|------|------------------|----------------------------|-----------------|
|    products|GET   |/products         |products_path               |products#index   |
|            |POST  |/products         |products_path               |products#create  |
| new_product|GET   |/products/new     |new_product_path            |products#new     |
|edit_product|GET   |/products/:id/edit|edit_products_path(@product)|products#edit    |
|     product|GET   |/products/:id     |product_path(@product)      |products#show    |
|            |PATCH |/products/:id     |product_path(@product)      |products#update  |
|            |PUT   |/products/:id     |product_path(@product)      |products#update  |
|            |DELETE|/products/:id     |product_path(@product)      |products#destroy |

其中，所有 GET 请求对应的 Action 都有相应视图。


