## cordova-plugin-alipay ##

Makes your Cordova application enable to use the [Alipay SDK](https://doc.open.alipay.com/docs/doc.htm?spm=a219a.7629140.0.0.hT44dE&treeId=54&articleId=104509&docType=1)
for mobile payment with Alipay App or Mobile Web. Requires cordova-android 4.0 or greater.

### ChangeLogs
  本cordova插件是基于支付宝App支付SDK的Demo实现
 - 升级支付宝SDK版本到20160825；
 - 修改了一些bug;
 - 支持Android和iOS Alipay SDK
###主要功能

 - 主要功能是：服务器把订单信息签名后，调用该插件调用支付宝sdk进行支付，支付完成后如支付成功，如果是9000状态，还要去服务端去验证是否真正支付

### Install 安装

The following directions are for cordova-cli (most people).  

* Open an existing cordova project, with cordova-android 4.0.0+, and using the latest CLI. TBS X5  variables can be configured as an option when installing the plugin
* Add this plugin

  ```sh
  cordova plugin add https://github.com/DreamMoon/cordova-plugin-alipay.git --variable PARTNER_ID=[你的商户PID可以在账户中查询]
  ```
  （对于android，可以不传PARTNER_ID）

   offline：下载后再进行安装 `cordova plugin add  YOUR_DIR`

### 支持平台

		Android IOS

### Android API

* js调用插件方法

```js

    //第一步：订单在服务端签名生成订单信息，具体请参考官网进行签名处理
    var payInfo  = "xxxx";

    //第二步：调用支付插件        	
    cordova.plugins.AliPay.pay(payInfo,function success(e){},function error(e){});

	 //e.resultStatus  状态代码  e.result  本次操作返回的结果数据 e.memo 提示信息
	 //e.resultStatus  9000  订单支付成功 ;8000 正在处理中  调用function success
	 //e.resultStatus  4000  订单支付失败 ;6001  用户中途取消 ;6002 网络连接出错  调用function error
	 //当e.resultStatus为9000时，请去服务端验证支付结果
	 			/**
				 * 同步返回的结果必须放置到服务端进行验证（验证的规则请看https://doc.open.alipay.com/doc2/
				 * detail.htm?spm=0.0.0.0.xdvAU6&treeId=59&articleId=103665&
				 * docType=1) 建议商户依赖异步通知
				 */

```

### ionic2 使用该插件的说明
  该cordova插件是源于 https://github.com/offbye/cordova-plugin-alipay 。
 - 在ionic2使用该插件的过程中所出现的问题进行解决（AliPay对象不存在的问题）：
  修改plugin.xml文件中的：
```xml

   <js-module src="www/AliPay.js" name="AliPay">
     	<!-- <clobbers target="cordova.plugins.AliPay" /> -->
	    <clobbers target="AliPay" />
    </js-module>

```
  
 - 修复ios中路径出错问题：
  plugin.xml
```xml

   <!-- <resource-file src="src/lib/AlipaySDK.bundle"/> -->
        <resource-file src="src/ios/lib/AlipaySDK.bundle"/>

```

###在ionic2中使用该插件
 - 1、按照上述的说明到ionic2项目中安装该插件；
 - 2、在需要使用该插件的.ts文件中的开头处（一般在 import 语句下面、@Commponent装饰器语句上面）声明AliPay对象：
```ts

   declare var AliPay: any;

```
 - 3、正式使用：在需要调用支付宝支付的地方，加入如下语句：
```ts
	//第一步：订单在服务端签名生成订单信息，具体请参考官网进行签名处理
	let payInfo  = "xxx";

	//第二步：调用支付插件
	AliPay.pay(payInfo,
	  function success(e){
	    alert('success!');
	  },function error(e){
	    alert('error!');
	  });
```

###应用该插件后ionic2打包为ios的注意事项
 - 应用该插件后，ionic2直接打包为android，支付宝支付的功能在app上使用是没有什么问题的。但是在ios上却没有那么顺利了，可能会出现这样的错误（在ionic build ios 或 xcode运行app项目的过程中）：
```
	Undefined symbols for architecture i386: “_deflate”, referenced from 
```

 - 解决方法的步骤如下：
1.go to target section；
2.Build Phases tab；
3.Link Binaries With Libraries；
4.click on + button；
5.search libz.tbd (or libz.dylib)；
6.click on add button.

 - 参考：http://stackoverflow.com/questions/18053546/undefined-symbols-for-architecture-i386-deflate-referenced-from-platcompres 
 
 
