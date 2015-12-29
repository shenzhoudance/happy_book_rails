# rails : csrf_token 与 ujs

rails 服务器，会为每个客户端，都生成一个token。  保存在session一份，保存在客户端一份（ html中的静态页面）
会生成一串字符串， 好长好复杂。o

```
<meta content="authenticity_token" name="csrf-param" />
<meta content="puSJVlcBd6C0BbPTyr74yifA1NMj8j+9ithVLpfLPxQ=" name="csrf-token" />
```

在提交表单的时候，这个字符串，会随之提交到后台。

后台，会根据 这个表单传递过来的token, 跟 本地sesion中保存的token相对比。
如果一致的话，rails就会接受这个表单。
如果token不一致，rails就会认为，有hacker在伪造表单。 拒绝接受。
（去年，有个新闻，年纪最小的黑客，用1块钱，买了1000块的东西)
通过伪造表单实现的。

refer to: http://guides.rubyonrails.org/security.html

rails使用 ujs  这个组件，来实现对 jquery的一些操作。
（对于 update, post, delete这3种 http请求时， 他们都要提交表单。于是，UJS就会自动把csrf token附加在 你的表单里，传递给服务器）

```
Parameters: {"utf8"=>"✓", "authenticity_token"=>"xGczWpIZJf5VlfAwa5BpaAW9wp7F3NvXzoirl2Zw7tI=", "decoratingcase"=>{"householder_id"=>"2", "province_code"=>"", "detailed_address"=>"", "house_area"=>"300平米", "house_type"=>"两室一厅", "house_buget"=>"40000", "expected_start_date"=>"2015-12-10", "actual_start_date"=>"", "end_date"=>"2015-12-30", "working_days"=>"5", "status"=>"未开工", "supervisor_id"=>"1", "foreman_id"=>"1"}, "current_city_code"=>"", "current_district_code"=>"", "commit"=>"确定", "id"=>"6"}
```
