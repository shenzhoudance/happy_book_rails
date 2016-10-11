# JSON

官方网站： http://www.json.org/

javascript object notation.  咱们知道它是用javascript 的结构，来表示数据结构就可以了。

你就认为 js 对象，就是一个 {} Hash 就可以了。

## 普通的对象

小王， 年龄18， 性别男，

```
{
   "name": "小王",
   "age": "18",
   "sex": "male"
}
```

格式： 跟javascript 的格式 “基本一样“。

1. "key": "value"
2. 每个不同的key-value之间，使用 "," 来连接。
3. 不能加注释。
4. 只能用双引号。不能用单引号。
5. 数据类型很少很少。

## 数据类型

### String

{
  "name": "小王"
}

上面的"小王" 就是字符串。

### 数字  (integer, float )

{
  "height": 1.86,
  "weight": 80
}

上面的  1.86 是float,  80 是integer, 总之，通称： 数字。 （不区分 double 这样的类型）

### 数组

{
  "name": "China",
  "provinces": ["Beijing", "Shanghai", "Guangzhou"]
}

上面的 provinces 的值就是数组

### Hash


```
{
  "name" : "小王",
  "parents": {
    "mother": {
      "name" : "王妈妈",
      "age": 46
    },
    "father" : {
      "name" : "王爸爸",
      "age": 46
    }
  }
}
```

上面的 parents , 就是一个大hash,  该hash 的key ：  mother, father
mother中也有两个key.  name/age.


### 最后来个混合

例如，我的一个接口， 要： 查询所有的books.
那么，查回来的结果如下：

```
{
  "result": [
    {
      "title": "马克思理论"
    },
    {
      "title": "恩格斯理论"
    }
  ]
}
```


## JSON用在哪里？

用在接口端。

谁来使用接口的数据呢？  手机app, 或者 H5的单页应用。

传统的web开发(php, jsp, asp)，不用接口。  例如：

```
<p><%= 1 + 1 %></p>
```
会被编译成：
```
<p>2</p>
```


## 如何解析JSON？





