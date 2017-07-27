# ThinkPHP-wxpay
3秒学会微信支付与thinkphp的结合运用

前言

只是提供一个简单的思路，当然你也可以选择通过建立自己的自定义function和class的形式来实现

我的习惯是把微信支付相关的function和class封装在ThinkPHP的Vendor

这是个demo，你可以学会微信支付新技能，当然也可以以拓展后的ThinkPHP为基础建立你的微信项目

在原来ThinkPHP目下引入封装了微信支付相关类和目录

解压后

只需要根据TP的MVC模式简单的调用就可以实现微信支付功能

但你必须得完成以下配置(网站根目录为App)


1.\ThinkPHP-wxpay\ThinkPHP\Library\Vendor\cert\ 下面放入你的商户平台支付验证文件，覆盖apiclient_cert.pem和apiclient_key.pem

2.\ThinkPHP-wxpay\ThinkPHP\Library\Vendor\lib\WxPayConfig.php 在此文件配置好你的相关参数（四个）

3.\ThinkPHP-wxpay\App\Common\Conf\config.php 配置好appid、appsecret

4.微信公众平台设置好微信支付url(注意大小写要一致,示例:http://你的域名/index.php/Home/Index/)

5.网页授权地址:你的域名


 IndexController.class.php
 <?php
namespace Home\Controller;
use Think\Controller;

/**
* @author CalvinPPD <www.5iweb.net>
* @version 1.0
* @create 2017/5/31
* @upd 2017/6/27
*/
class IndexController extends Controller {
	
	public function wxpay(){
			import('Vendor.JSSDK');
			Vendor('WxPayJsApiPay');
			Vendor('log');
			Vendor('lib.WxPayApi');
			
			//实例化JSSDK
			$jssdk=new \JSSDK(C('appid'),C('appSecret'));
			//获取数据
			$signPackage =$jssdk->GetSignPackage();
			//初始化日志
			$logHandler= new \CLogFileHandler("./logs/".date('Y-m-d').'.log');
			$loginfo=\Log::Init($logHandler, 15);
			
			//①获取用户openid
			$tools=new \JsApiPay();
			$openId = $tools->GetOpenid();
			
			
			
			//②统一下单
			$input=new \WxPayUnifiedOrder();
			$input->SetBody("5iweb.net");
			$input->SetAttach("5iweb.net");
			$input->SetOut_trade_no(\WxPayConfig::MCHID.date("YmdHis"));
			$input->SetTotal_fee("1");
			$input->SetTime_start(date("YmdHis"));
			$input->SetTime_expire(date("YmdHis",time()+ 600));
			$input->SetGoods_tag("5iweb.net");
			$input->SetNotify_url("http://www.5iweb.net");
			$input->SetTrade_type("JSAPI");
			$input->SetOpenid($openId);
			$order = \WxPayApi::unifiedOrder($input);
			$jsApiParameters=$tools->GetJsApiParameters($order);
	
			$this->assign('jsApiParameters',$jsApiParameters);
			$this->assign('signPackage',$signPackage);
			$this->display();
		
	}
	
	
}



wxpay.html
<!DOCTYPE html>
<html>
  <script>
    (function (doc, win) {
      var docEl = doc.documentElement,
          resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
          recalc = function () {
              var clientWidth = docEl.clientWidth;
              if (!clientWidth) return;
              if(clientWidth>=640){
                  docEl.style.fontSize = '100px';
              }else{
                  docEl.style.fontSize = 100 * (clientWidth / 640) + 'px';
              }
          };

      if (!doc.addEventListener) return;
      win.addEventListener(resizeEvt, recalc, false);
      doc.addEventListener('DOMContentLoaded', recalc, false);
    })(document, window);
  </script>
	<head>
	<meta charset="utf-8">
	<meta name="viewport" content="user-scalable=no, width=device-width, initial-scale=1, maximum-scale=1">
	
    <script src="https://res.wx.qq.com/open/js/jweixin-1.2.0.js" type="text/javascript" charset="utf-8"></script>
	<title>wxpay</title>
    </head>
  <body>
	
		<div class="pay" style="margin:0 auto">
			<img src="{$Think.config.IMG_URL}payend.png"/ id="zhifu" style="margin:0 auto;width:237px;height:72px;">
		</div>
  </body>
  <script>
  	onload=function() {
		  document.getElementById('zhifu').addEventListener('touchstart',function() {
			  	if (typeof WeixinJSBridge == "undefined"){
				    if( document.addEventListener ){
				      document.addEventListener('WeixinJSBridgeReady', jsApiCall, false);
				    }else if (document.attachEvent){
				      document.attachEvent('WeixinJSBridgeReady', jsApiCall); 
				      document.attachEvent('onWeixinJSBridgeReady', jsApiCall);
				    }
			  	}else{
			   	 	jsApiCall();
			  	}
				function jsApiCall() {
					WeixinJSBridge.invoke(
					    'getBrandWCPayRequest',
					    {$jsApiParameters},
					    function(res){
					      WeixinJSBridge.log(res.err_msg);
					      if(res.err_msg.indexOf('ok')!=-1){
					      	localStorage.clear();
					   		location.href='{:U('index')}'
					      }
					    }
					);
				}
		  }); 		
  	}
	</script>
	
 </html>

