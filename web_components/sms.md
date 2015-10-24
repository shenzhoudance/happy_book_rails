#发送短信
开发中，我们经常需要和用户校验身份，或者实时的通知用户一些重要的信息，短信是比较方便直接的方式。下面介绍怎样给用户发送短信。
这里我们使用[阳洋传媒](http://www.ipyy.net/)的短信服务为例([华信科技](http://www.ipyy.com/)方法类似)。其提供的HTTP借口的方式完成信息发送，我们只需将特定参数以`POST`请求的方式访问给定的接口即可完成短信发送。

群发短信：
```javascript
//titanium demo
var client = Ti.Network.createHTTPClient();
client.open("POST","http://ipyy.net/WS/BatchSend.aspx");
var parameters = {
  CorpID: "用户ID",
  Pwd: "用户密码",
  Mobile: "接收号码，多个用逗号隔开，最多600个",
  Content: "短信内容",
  Cell: "子号（可为空）",
  SendTime: "定时发送时间(为空标示立即发送)"
}
client.send(parameters);
client.onload = function(){
  do_when_onload;
}
```
其他业务参数格式[HTTP接口](http://www.ipyy.net/http.asp)

其他短信平台使用方法类似。
