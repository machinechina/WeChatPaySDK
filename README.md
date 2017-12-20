### 功能说明: ###
根据配置的路由和浏览器环境产生支付页面
`微信端`:生成JsApi支付页面(支持iframe,会先出现父页面,再跳转,请考虑页面设计)
`非微信端`:生成扫码支付的二维码(支持iframe)
两种方式在支付成功后会跳转到{afterPaymentRedirect}指定的地址(如果是iframe则父页面跳转)
支付链接格式为:{hostAndVirtualPath}/{wechatMapPath}/{wechatAppId}?orderId=xxx

### 微信配置: ###
#### 公众号登录:(mp.weixin.qq.com) ####
`网页授权域名`: 填写发起支付的站点域名
`JS 接口安全域名`: 填写发起支付的站点域名
#### 商户平台登录（pay.weixin.qq.com） ####
`支付授权目录`: 填写发起jsApi支付的目录,填写{hostAndVirtualPath}/{wechatMapPath}/

### 代码整合: ###
`AspNet MVC`: 引用WeChatPaySDK.dll,然后在StartUp中配置:
``` cs
  public void Configuration(IAppBuilder app)
  {
	 app.AddWxPay(
                hostAndVirtualPath:支付发起域名,包括虚拟目录.如"http://domain/virtualPath"
                wechatMapPath: SDK生成的支付路由，必须‘/’开头,如"/wechat/pay",
		configs: 微信支付账号的设置,WxPayConfig类型,支持多个账号,以params 方式传入,包括以下配置:
		wechatAppId: 微信appId,
		wechatKey: 微信支付key,
		wechatMchId: 微信商户号,
		wechatAppSecret: 微信appSecret,
		getOrderInfo: orderId => 调用者返回一个OrderForPayment对象,用以获取获取订单费用（单位：分）和产品标示,
		afterPayment: orderFromPayment => 调用者在支付成功后进行的订单操作(修改订单状态等),orderFromPayment包括订单号,微信订单号,订单金额
		orderStatusQuery: orderId => 调用者提供的订单状态查询接口(true/false),供前台轮询,成功后跳转到afterPaymentRedirect
		afterPaymentRedirect: 支付完成后的跳转路由(订单列表页等),如"/order/list",附带跳转前的queryString
	);
  }
```
