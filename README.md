#配置

下载文件解压后把lib文件夹放到ThinkPHP\Library\Vendor\目录下

lib/WxPayConfig.php配置好你的appid、appsecret、mchid和key（来自微信支付开后我的商户平台）

lib/cert/下的两个微信支付安全证书,在你的商户平台找到他们并下载覆盖

在微信公众平台配置好微信支付URL（http://域名/index.php/Home/Index/）和网页授权域名

网站根目录为APP文件夹

