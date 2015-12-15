# 关于密码校验


## 密码的产生过程。

明文 -> md5(明文）  ->  'a1b2c3d4 .... z20' -> 保存到数据库。

永远不要把明文密码保存在数据库中。永远要使用md5加密的方式。


在rails/ java 等 框架中，正确的做法是： md5加密是框架自动做的。

例如：

user = User.create  :name => 'Jim',  :password => '123456'
然后我们登录到MYSQL 查看password 这个列的时候，会发现，已经被自动加密过了。

## WEB 端， 也不是每次都需要校验密码。

靠的是： session.

session: 会话。保存在服务器端的状态数据。
可以认为，服务器跟客户端保持的一种持续的状态。（例如： 30分钟内不需要登录）

例如： 对于某个论坛，如果当前有3个人在线，服务器端，对就会专门记录这三个客户端的session_id:

```
sessions:
  1#:
    session_id: a1b2c3d4...
    is_logged_in: true

  2#: e1f2g3...
    is_logged_in: nil
  3#: e1f2g4....

```

总之，每个session 都不一样。
从上面的代码中，如果对于同样的一段erb代码（服务器端）:

```ruby
<%=  session[:is_logged_in] %>
```

在1# 客户端上， 就会显示：  true
在2#， 3#客户端上，就会显示：    （空）

cookie:

保存在客户端的变量。

## Devise 中的密码体系

例如，我们有个 User 表，它由devise 进行控制。那么，默认这个表(User)有若干列：

1. email
2. encrypted_password

然后，user 还有个属性（attribute) :   password

所以，每当我们新建一个model的时候， ：

User.create :email => 'someone@fake.com', :password => '88888888'

于是，新的user 记录就会自动生成，并且，数据库中它的encrypted_password 会根据 '88888888'来
自动生成。

2. 根据上面生成的记录， devise 还能自动的根据encrypted_password, 得到真正的password.

user = User.last # 获取到刚才新建的用户
user.password  # => 88888888

所以，在devise所作用的user表中，是没有password这个列存在的,它是一个独立的属性, 可以
供我们访问。

## 移动端，不是每次都需要密码。
大家可以认为：在移动端，没有session这个概念。

## 如何在移动端，判断当前用户是否正确登录？

1. 身份的验证。  客户端： 发送 用户名 + 密码， 服务器端认证。
2. 每次由 客户端，向 服务器端，发送请求的时候，要附带一段 “密码字符串”，它的作用等同于密码。

例如：
1. 身份验证后：

app ：  /interface/login?name=xiaowang&password=123456

服务器端： 对该request处理。 正确的话，返回：

```
{
  result: 'success',
  token: 'abc'
}
```

2. 接下来，app端，就把该token保存在本地数据库，然后，每次发送请求到服务器时，
都附带上这个token:

app:

GET  /interface/create_case?token=abc
GET  /interface/delete_case?id=3&token=abc


这样的话，我们在服务器端，每次都可以针对一个request做验证：

```ruby

class Interface < ActionController::Base

  def create_case
    if params[:token].blank? || params[:token].is_invalid?
      render :json => {
        message: '您没有登录'
      }
    end
    Case.create ....
    render :json => {
      result: 'success',
      ...
    }
  end

end
```

在实际的操作当中，如果严格一些的话，每次发起的请求，不仅有token ,还应该有
md5的值，有当前的时间戳。有pid ... guid ...

## 如果根据 token找到对应的用户呢？

User表：

id  | name  |   encrypted_password  | token
1   | jim   |   a1b2c3...           | abc
2   | Lilei |   c3d4e5...           | efg

token是可以变的。我们可以让它具有一定的时效性：

改良后的：User表：

id  | name  |   encrypted_password  | token | expires_at
1   | jim   |   a1b2c3...           | abc   | 2015-10-30
2   | Lilei |   c3d4e5...           | efg   | 2015-11-5

那么，这样的话，我们就可以根据某个user记录的expires_at 来判断，该用户的token是否有效。

然后，在每次登录的时候，更新 token 和 expires_at .


## 所以app的发起request，要有一个实现模式

1. 要有一个统一的方法来发起get/post 请求


// 就是确保每次发起请求的时候，都要给到服务器端：  token,pid, guid....
function send_request(url){

  final_url =  url + "&token=" + get_token() + "&pid=" + get_pid() ...
  // 把这个final_url 发送出去
}
