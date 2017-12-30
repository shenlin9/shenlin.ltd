---
title: 细说 PHP - 图像处理
categories:
  - PHP
tags:
  - PHP
---

PHP图像处理

	首先安装GD库 ---lamp环境安装
	php配置文件中设置php_gd2.dll
	利用phpinfo查看是否支持gd库

	一、画图
		验证码、统计图

		1、创建画布，为一资源类型，需要指定高度和宽度

			imagecreate();
			imagecreatetruecolor();	//推荐使用

			$img=imagecreatetruecolor(200,200);

		2、填充颜色

			imagecolorallocate();//和imagecreate()搭配使用
			imagefill(); 
			
			$red=imagecolorallocate(255,0,0);
			$green=imagecolorallocate(0,255,0);
			imagefill($img,0,0,);

		3、绘制图像，可绘制：
			矩形，圆、点、线段、扇形、字符、字符串、freetype字体
			上述每一个图像对应一个函数

			imagefilledrectangle
			imagerectangle
			imageline
			imagesetpixel
			imageellipse
			imagefilledellipse
			imagefilledarc

			imagefilledrectangle($img,10,10,80,80,$green);

			imagechar
			imagecharup
			imagestring
			imagestringup
			imagettftext
			iconv
				汉字gb2312的需要转换为utf-8
				使用非php的内置字体最好将字体文件复制到程序目录下
		4、输出图像或保存成文件
			输出各种类型:gif、jpg、png
			imagegif();
			imagejpeg();
			imagepng();

			header("Content-type:image/gif");
			imagegif($img);
		5、释放资源
			imagedestroy($img);

	二、处理原有图像
		缩放、加水印、电子相册

		imagetypes()

		if(imagetypes() & IMG_GIF){
			header("Content-type:image/gif");
			imagegif($this=>image);
		}

图片处理

	裁剪、缩放、翻转、锐化、透明、旋转	

	1、创建图片资源
		imagecreatefromgif(图片名称);
		imagecreatefrompng(图片名称);
		imagecreatefromjpeg(图片名称);

		imagesx();
		imagesy();
		getimagesize();
	2、图形处理
		裁剪、缩放：imagecopyresized
		等比例裁剪、缩放：imagecopyresampled
			 固定方案：66节 43:05
		透明处理：png、jpeg透明色都正常，只要gif不正常
			 imagecolortransparent();
			 imagecolorstotal();
		 	 imagecolorsforindex();
			 固定方案：66节 43:05
		加水印：文字水印、图片水印
			 imagecopy();
		旋转：imagerotate();
		翻转：水平翻转、垂直翻转
			无系统函数，使用的是逐行复制图片的方式
		锐化、浮雕：imagecolorsforindex();
			imagecolorat();
			imagecolorexact();
			无系统函数，逐个获取图像像素，颜色加重后放入新图像

		开发中常用为缩放和加水印。
	3、保存图片
		imagegif();
		imagepng();
		imagejpeg();

