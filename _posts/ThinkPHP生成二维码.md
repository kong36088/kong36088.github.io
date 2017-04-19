title: ThinkPHP生成二维码
categories: PHP##分类
tags: [ThinkPHP,验证码]##标签，多标签格式为 [tag1,tag2,...]
keywords: ThinkPHP,验证码##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 在php中，大致生成二维码的方法有两种：一种是引用别家提供的api然后进行自动生成，google有提供api专门生成二维码，而国内的新浪也有api专门提供，················
date: 2015/11/16 19:24:25 
---
在php中，大致生成二维码的方法有两种：一种是引用别家提供的api然后进行自动生成，

google有提供api专门生成二维码，而国内的新浪也有api专门提供，

新浪的api地址是：http://qrcoder.sinaapp.com?t=http://www.gbtags.com

t=后面加上想要生成的url即可

生成二维码的另一种方法：通过开源的phpqrcode进行自动生成。

<!--more-->

以下是源码：

``` php
protected function qrcode($data, $filename=false, $picPath = false, $logo = false, $size = '4', $level = 'L', $padding = 2, $saveandprint = false) {
	/*
	 * function qrcode(){
	 * $filename='qrcode.png';
	 * $logo=SITE_PATH."\\Public\\Home\\images\\logo_80.png";
	 * qrcode('http://www.dellidc.com',$filename,false,$logo,8,'L',2,true);
	 * }
	 *
	 * @param $data 二维码包含的文字内容
	 * @param $filename 保存二维码输出的文件名称，*.png
	 * @param bool $picPath 二维码输出的路径
	 * @param bool $logo 二维码中包含的LOGO图片路径
	 * @param string $size 二维码的大小
	 * @param string $level 二维码编码纠错级别：L、M、Q、H
	 * @param int $padding 二维码边框的间距
	 * @param bool $saveandprint 是否保存到文件并在浏览器直接输出，true:同时保存和输出，false:只保存文件
	 * return string
	 */
	vendor ( "phpqrcode.phpqrcode" ); // 引入工具包
	// 下面注释了把二维码图片保存到本地的代码,如果要保存图片,用$fileName替换第二个参数false
	$path = $picPath ? $picPath : SITE_PATH . "\\Uploads\\Picture\\QRcode"; // 图片输出路径
	mkdir ( $path );
	// 在二维码上面添加LOGO
	if (empty ( $logo ) || $logo === false) { // 不包含LOGO
		if ($filename == false) {
			\QRcode::png ( $data, false, $level, $size, $padding, $saveandprint ); // 直接输出到浏览器，不含LOGO
		} else {
			$filename = $path . '/' . $filename; // 合成路径
			\QRcode::png ( $data, $filename, $level, $size, $padding, $saveandprint ); // 直接输出到浏览器，不含LOGO
		}
	} else { // 包含LOGO
		if ($filename == false) {
			// $filename=tempnam('','').'.png';//生成临时文件
			die ( '参数错误' );
		} else {
			// 生成二维码,保存到文件
			$filename = $path . $filename; // 合成路径
		}
		\QRcode::png ( $data, $filename, $level, $size, $padding );
		$QR = imagecreatefromstring ( file_get_contents ( $filename ) );
		$logo = imagecreatefromstring ( file_get_contents ( $logo ) );
		$QR_width = imagesx ( $QR );
		$QR_height = imagesy ( $QR );
		$logo_width = imagesx ( $logo );
		$logo_height = imagesy ( $logo );
		$logo_qr_width = $QR_width / 5;
		$scale = $logo_width / $logo_qr_width;
		$logo_qr_height = $logo_height / $scale;
		$from_width = ($QR_width - $logo_qr_width) / 2;
		imagecopyresampled ( $QR, $logo, $from_width, $from_width, 0, 0, $logo_qr_width, $logo_qr_height, $logo_width, $logo_height );
		if ($filename === false) {
			Header ( "Content-type: image/png" );
			imagepng ( $QR );
		} else {
			if ($saveandprint === true) {
				imagepng ( $QR, $filename );
				header ( "Content-type: image/png" ); // 输出到浏览器
				imagepng ( $QR );
			} else {
				imagepng ( $QR, $filename );
			}
		}
	}
	return $filename;
}
``` 

其中，在使用该类的时候必须把下载到的phpqrcode文件夹放入ThinkPHP\Library\Vendor目录下，大致的路径是ThinkPHP\Library\Vendor\phpqrcode

phpqrcode的下载地址是：[点我](http://sourceforge.net/projects/phpqrcode/files/releases/)
