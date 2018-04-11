# Web

## 微信支付
### 1.公众号支付
```
交互细节：

以下是支付场景的交互细节，请认真阅读，设计商户页面的逻辑：

（1）用户打开商户网页选购商品，发起支付，在网页通过JavaScript调用getBrandWCPayRequest接口，发起微信支付请求，用户进入支付流程。

（2）用户成功支付点击完成按钮后，商户的前端会收到JavaScript的返回值。商户可直接跳转到支付成功的静态页面进行展示。

（3）商户后台收到来自微信开放平台的支付成功回调通知，标志该笔订单支付成功。

注：（2）和（3）的触发不保证遵循严格的时序。JS API返回值作为触发商户网页跳转的标志，但商户后台应该只在收到微信后台的支付成功回调通知后，才做真正的支付成功的处理。
```
##### 支付所需的代码
```
function onBridgeReady() {
        WeixinJSBridge.invoke(
           'getBrandWCPayRequest', {
                "appId": "",//公众号名称，由商户传入 
                "timeStamp": "1395712654",//时间戳，自1970年以来的秒数 
                "nonceStr": "e61463f8efa94090b1f366cccfbbb444", //随机串
                "package": "prepay_id=u802345jgfjsdfgsdg888",
                "signType": "MD5",         //微信签名方式：     
                "paySign": "70EA570631E4BB79628FBCA90534C63FF7FADD89" //微信签名 
            },
            function (res) {
                if (res.err_msg == "get_brand_wcpay_request:ok") {
                    // 使用以上方式判断前端返回,微信团队郑重提示：
                    //res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
                } else if (res.err_msg == "get_brand_wcpay_request:cancel") {
                    //支付过程中用户取消
                } else if (res.err_msg == "get_brand_wcpay_request:fail") {
                    //支付失败
                }
            }
        );
    }
```
### 2.微信JS-SDK
#### 概述
微信JS-SDK是微信公众平台 面向网页开发者提供的基于微信内的网页开发工具包。<br>
                通过使用微信JS-SDK，网页开发者可借助微信高效地使用拍照、选图、语音、位置等手机系统的能力，同时可以直接使用微信分享、扫一扫、卡券、支付等微信特有的能力，为微信用户提供更优质的网页体验。<br>
#### JSSDK使用步骤
##### 步骤一：绑定域名
先登录微信公众平台进入“公众号设置”的“功能设置”里填写“JS接口安全域名”。

备注：登录后可在“开发者中心”查看对应的接口权限。

##### 步骤二：引入JS文件
在需要调用JS接口的页面引入如下JS文件，（支持https）：[http://res.wx.qq.com/open/js/jweixin-1.2.0.js](http://res.wx.qq.com/open/js/jweixin-1.2.0.js)

备注：支持使用 AMD/CMD 标准模块加载方法加载

##### 步骤三：通过config接口注入权限验证配置
所有需要使用JS-SDK的页面必须先注入配置信息，否则将无法调用（同一个url仅需调用一次，对于变化url的SPA的web app可在每次url变化时进行调用,目前Android微信客户端不支持pushState的H5新特性，所以使用pushState来实现web app的页面会导致签名失败，此问题会在Android6.2中修复）。
```
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
```
##### 步骤四：通过ready接口处理成功验证
```
wx.ready(function(){
    // config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
});
```
    
##### 步骤五：通过error接口处理失败验证
```
wx.error(function(res){
    // config信息验证失败会执行error函数，如签名过期导致验证失败，具体错误信息可以打开config的debug模式查看，也可以在返回的res参数中查看，对于SPA可以在这里更新签名。
});
```
#### 基础接口

#### 微信支付
发起一个微信支付请求<br>
```
wx.chooseWXPay({
timestamp: 0, // 支付签名时间戳，注意微信jssdk中的所有使用timestamp字段均为小写。但最新版的支付后台生成签名使用的timeStamp字段名需大写其中的S字符
nonceStr: '', // 支付签名随机串，不长于 32 位
package: '', // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*）
signType: '', // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
paySign: '', // 支付签名
success: function (res) {
// 支付成功后的回调函数
}
});
```
备注：prepay_id 通过微信支付统一下单接口拿到，paySign 采用统一的微信支付 Sign 签名生成方法，注意这里 appId 也要参与签名，appId 与 config 中传入的 appId 一致，即最后参与签名的参数有appId, timeStamp, nonceStr, package, signType。<br>
微信支付开发文档：[https://pay.weixin.qq.com/wiki/doc/api/index.html](https://pay.weixin.qq.com/wiki/doc/api/index.html)
    
    
