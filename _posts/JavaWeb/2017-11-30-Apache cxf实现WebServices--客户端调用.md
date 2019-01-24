---
layout: post
title:  "Apache cxf实现WebServices--客户端调用"
date:   2017-11-30
desc: "Apache cxf实现WebServices--客户端调用"
keywords: "java web,cxf"
categories: [javaweb]
tags: [java web, WebServices, cxf]
---

# Apache cxf实现WebServices客户端调用

这个跟我前面写的Apache cxf实现WebServices服务端是相关的，可以 通信。

		import org.apache.cxf.endpoint.Client;
		import org.apache.cxf.jaxws.endpoint.dynamic.JaxWsDynamicClientFactory;		


		// 创建动态客户端
		JaxWsDynamicClientFactory clientFactory = JaxWsDynamicClientFactory.newInstance();
		// 创建客户端连接
		Client client=clientFactory.createClient("http://localhost:8081/scm_server/services/testService?wsdl");
		Object[] result;
		// 调用客户端invoke()方法，把参数传递给要调用的方法并取得结果对象
		result = client.invoke("saveUser",new Object[]{id,username});
		return result[0].toString();

(saveUser) 服务端的函数名;
(id,username)  函数的参数

相关jar包下载：http://download.csdn.net/download/xdsxhdyy/10136638
