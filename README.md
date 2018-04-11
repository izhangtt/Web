# Web

### 微信支付（公众号支付）
```
交互细节：

以下是支付场景的交互细节，请认真阅读，设计商户页面的逻辑：

（1）用户打开商户网页选购商品，发起支付，在网页通过JavaScript调用getBrandWCPayRequest接口，发起微信支付请求，用户进入支付流程。

（2）用户成功支付点击完成按钮后，商户的前端会收到JavaScript的返回值。商户可直接跳转到支付成功的静态页面进行展示。

（3）商户后台收到来自微信开放平台的支付成功回调通知，标志该笔订单支付成功。

注：（2）和（3）的触发不保证遵循严格的时序。JS API返回值作为触发商户网页跳转的标志，但商户后台应该只在收到微信后台的支付成功回调通知后，才做真正的支付成功的处理。
```
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
