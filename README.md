#前言

只是提供一个简单的思路，当然你也可以选择通过建立自己的自定义function和class的形式来实现，我的习惯是把微信支付相关的function和class封装在ThinkPHP的Vendor目录下。这是个demo，你可以学会如何通过thinkphp实现微信支付，当然也可以以拓展后的ThinkPHP为基础建立你的微信项目。

#配置

在原来ThinkPHP目下引入封装了微信支付相关类和目录。解压后，只需要根据TP的MVC模式简单的调用就可以实现微信支付功能。

但你必须得完成以下配置(网站根目录为App)

1.\ThinkPHP-wxpay\ThinkPHP\Library\Vendor\cert\ 下面放入你的商户平台支付验证文件，覆盖apiclient_cert.pem和apiclient_key.pem

2.\ThinkPHP-wxpay\ThinkPHP\Library\Vendor\lib\WxPayConfig.php 在此文件配置好你的相关参数（四个）

3.\ThinkPHP-wxpay\App\Common\Conf\config.php 配置好appid、appsecret

4.微信公众平台设置好微信支付url(注意大小写要一致,示例:http://你的域名/index.php/Home/Index/)

5.网页授权地址:你的域名

