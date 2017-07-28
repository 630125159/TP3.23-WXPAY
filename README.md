#配置
下载文件解压后把lib文件夹放到ThinkPHP\Library\Vendor\目录下
lib/WxPayConfig.php配置好你的appid、appsecret、mchid和key（来自微信支付开后我的商户平台）
lib/cert/下的两个微信支付安全证书,在你的商户平台找到他们并下载覆盖
在微信公众平台配置好微信支付URL（http://域名/index.php/Home/Index/）和网页授权域名
网站根目录为demo文件夹
#代码
<?php
namespace Home\Controller;
use Think\Controller;
class IndexController extends Controller {
    public function wxpay(){
            Vendor('lib.JSSDK');
            Vendor('lib.WxPayJsApiPay');
            Vendor('lib.log');
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
