# obtrusive js v.s. unobtrusive js

## obtrusive js: 显式的js
```
<div onclick='alert("hihihi"); a = 1+1; c = 2*2; ' > some text here </div>
```

1. 难于查看（特别是多行代码）    ( hard to view the code)
2. 难于单元测试   ( hard to write unit test)
3. JS 跟ＨＴＭＬ 混在了一起。 ( mixed js with html... )

## unobtrusive js: 隐式的js

<div class='alert_hi'>some text here</div>
<div class='alert_hi'> anotherr text ehrre.... </div>

<script>
$('.alert_hi').onclick = function (){ alert('hihihi') }
</script>

1. 解耦： ＪＳ 代码与ＨＴＭＬ代码  (decoupled js and html)
2. 方便单元测试。 (比如上面，直接对ＦＯＯ进行测试 ）  (easy to test)
3. 提高代码复用程度。  (easy to reuse)
4. 高手都这么用。 ( js gurus way)







# 明星框架

public/code 下面，  要针对不同的app ， 有不同的folder： code_of_app1, code_of_app2

case_detail.js            list_case_details ?  show_case_details?
fitment_manage.js         decoration_list
increase_progress.js      create_case_details
my_fitment_detail.js      house_holder/case_details
my_house_detail.js        house_holder/case_full_information
photo_rule.js             rules_of_taking_photo
work_detail.js            supervisor/case_details


detail  不是一个词。
details  才是一个词，这个词是单数。


#  H5端： 可以做成 跟app 端工程差不多的东西。
要求：
不是普通的web页面

大家做手机端 h5的时候，记住一个规则： 请求越少越好。

因为，手机h5页面的最大痛点：打开的太慢。
我们的原则是： 一秒内打开。
所以，理想的h5页面：  one page app.  一次加载(css， js， html），以后再也不需要刷新整个页面。
angular: 第一次加载：  300k
react:  100k
vuejs:  33k
