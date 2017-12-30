---
title: 细说 PHP - Ajax
categories:
  - PHP
tags:
  - PHP
---

Ajax介绍和引擎对象的创建

	什么是Ajax
		需要有Javascript和DOM的基础
		Ajax（Asynchronous Javascript and XML)
		采用异步交互过程

	Ajax优缺点
		按需求数据，减轻服务器负担
		减少用户等待时间，优化用户体验

		需要测试对各浏览器的兼容性
		后退功能失效
		流媒体不能很好支持
		嵌入式设备不能很好支持，如手机


	Ajax应用
		局部刷新

	创建Ajax对象
		为兼容各浏览器，创建对象稍复杂，但代码是固定的
		将创建XMLHttpRequest对象的过程写到一个函数中
		将浏览器分成两种：
				IE系列：ie5,ie5.5,ie6,ie7,ie8
				非IE系列：都是按W3C标准 firefox mozilla opera

		function createAjax(){
			var request=false;
			
			//window对象中有XMLHttpRequest则是：ie7、ie8或非ie浏览器
			if(window.XMLHttpRequest){
				alert("ie7、ie8或非ie浏览器");

				request=new XMLHttpRequest();

				//有的浏览器需要重载MIME类型
				if(request.overrideMimeType){
					request.overrideMimeType("text/xml");
				}
			}

			//window对象中友ActiveXObject即是IE的低版本
			if(window.ActiveXObject){
				alert("IE的低版本");

				//不同版本有不同参数
				ver versions=['Microsoft.XMLHttp','MSXML.XMLHTTP','Msxml2.XMLHTTP.7.0','Msxml2.XMLHTTP.6.0','Msxml2.XMLHTTP.5.0','Msxml2.XMLHTTP.4.0','MSXML2.XMLHTTP.3.0','MSXML2.XMLHTTP'];

				for(var i=0;i<version.length;i++){
					try{
						request=new ActiveXObject(version[i]);
						if(request){
							return request;
						}
					}catche(e){
						request=false;
					}
				}
			}

			return request;
		}


使用Ajax对象中的属性和方法完成对服务器的请求和响应

	请求是使用ajax对象的方法实现，接收返回是使用Ajax对象的属性实现。

	6个属性：
		onreadystatechange	状态改变的事件触发器
		readyState		对象状态
					0=未初始化 1=读取中 2=已读取 3=交互中 4=完成
		responseText		服务器返回数据的文本版本
		responseXML		服务器返回数据的兼容DOM的XML文档对象
		status			服务器返回的状态码，如：404=文件未找到 200=成功
		statusText		服务器返回的状态文本信息

	6个方法：
		getAllResponseHeaders()：以字符串返回完整头信息
		getResponseHeader("headerlabel")：以字符串返回单个的header标签
		setRequestHeader("label","value")：设置header并和请求一起发送
		
		open("method","URL"[,asyncFlag[,"userName"[,"password"]]])：设置请求参数
		send(content)：发送请求
		abort()：停止当前请求

	//注意：每次请求都要使用一个新的XMLRequest对象
	var ajax=null;

	function show(){
		ajax=createAjax();
		ajax.onreadystatechange=function(){
			if(ajax.readyState==4){
				if(ajax.status==200){
					alert(ajax.responseText);
				}else{
					alert("页面请求失败");
				}
			}
		}	
		ajax.open("get","server.php?key=value&"+math.random(),true); //get直接通过url传数据
		ajax.send(null);

		//上面普通文本，下面xml文档对象

		ajax-createAjax();
		ajax.onreadystatechange=function(){
			if(ajax.readyState==4){
				if(ajax.status==200){
					var dom=ajax.responseXML;
					var users=dom.getElementByTagName("user");
					alert(users.length);
				}else{
					alert("页面请求失败");
				}
			}
		}	
		ajax.open("post","server.php",true);
		ajax.setRequestHeader("Content-Type","application/x-www-form-urlencoded");//指定传输编码
		ajax.send("key=value&"+math.random());
	}

	注意：有中文时，为防止乱码，最好在服务器返回信息时设置header：
		header("Content-Type:text/html;charset=utf-8");

	服务器端返回json编码：
		echo json_encode(array("one"=>"111","two"=>"222"));

	客户端处理json编码：
		eval("var obj=" + ajax.responseText);
		alert(obj.one);
		alert(obj.two);

自定义对象简化Ajax的操作（*******自己实现一遍******）

	function Ajax(type){
		var ajax = new object();

		ajax.get = function(){

		}

		ajax.post = function(){

		}
	}


使用Ajax实现无刷新分页

	为ajax取过的数据增加客户端缓存


