title: 关于ping++对接的一点看法
categories: PHP##分类
tags: [PHP,分页]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,分页##文章关键词，多关键词格式为 keyword1,keywords2,...
description: ping++支付接入的介绍
date: 2015/11/16 18:24:25 
---

首先是处理支付方式的一个方法：
``` php
function deal_way($channel) {
	$extra = array ();
	switch ($channel) {
		case 'alipay_wap' :
			$extra = array (
					'success_url' => 'http://127.0.0.1/index.php/Home/Index/success',
					'cancel_url' => 'http://127.0.0.1/index.php/Home/Index/cancel' 
			);
			break;
		case 'upmp_wap' :
			$extra = array (
					'result_url' => 'http://127.0.0.1/index.php/Home/Index/result?code=' 
			);
			break;
		case 'bfb_wap' :
			$extra = array (
					'result_url' => 'http://127.0.0.1/index.php/Home/Index/result?code=',
					'bfb_login' => true 
			);
			break;
		case 'upacp_wap' :
			$extra = array (
					'result_url' => 'http://127.0.0.1/index.php/Home/Index/result?code=' 
			);
			break;
		case 'wx_pub' :
			$extra = array (
					'open_id' => 'Openid' 
			);
			break;
		case 'wx_pub_qr' :
			$extra = array (
					'product_id' => 'Productid' 
			);
			break;
		default :
			break;
	}
	return $extra;
}

``` 
接下来是创建charge对象的php代码：
``` php
public function get_charge() {
	$channel = 'alipay_wap';
	$extra = deal_way ( $channel );
	\Pingpp\Pingpp::setApiKey ( 'sk_test_CijTyHCmzzb5GerTSG5m9qHK' );
	$charge = \Pingpp\Charge::create ( array (
			'subject' => 'Your Subject',
			'body' => 'Your Body',
			'amount' => 10000,
			'order_no' => 111111111,
			'currency' => 'cny',
			'extra' => $extra,
			'channel' => $channel,
			'client_ip' => '127.0.0.1',
			'app' => array (
					'id' => 'app_SabjH8bjzHa1XLyr' 
			) 
	) );
	$this->response ( $charge );
}
``` 
 

最后是ajax的写法：
``` javascript
function wap_pay(channel) {
	var amount = document . getElementById ( 'amount' ) . value * 100;
	var send_data="channel="+channel+"&amount="+amount;
	var xhr = new XMLHttpRequest();
	xhr.open("POST", "!:C('PAY')!/Pay/get_charge", true);
	xhr.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
	xhr.send(send_data);
	xhr.onreadystatechange = function () {
		if (xhr.readyState == 4 && xhr.status == 200) {
			console.log(xhr.responseText);
			pingpp.createPayment(xhr.responseText, function(result, err) {
				console.log(result);
				console.log(err);
			});
		}
	}
}
``` 
在这里要注意，ajax的Content-Type要写成application/x-www-form-urlencoded