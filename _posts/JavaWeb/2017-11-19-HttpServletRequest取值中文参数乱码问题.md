---
layout: post
title:  "HttpServletRequest取值中文参数乱码问题"
date:   2017-11-17
desc: "HttpServletRequest取值中文参数乱码问题"
keywords: "java web,HttpServletRequest"
categories: [javaweb]
tags: [java web, HttpServletRequest]
---

# HttpServletRequest取值中文参数乱码问题

可以获取从前台传递过来的参数


1、提交方式为post，后台获取值

    String userName = request.getParameter("userName");

若出现乱码，则是前台和后台的编码格式不一致引起的，一般前台JSP都是采用UTF-8的编码格式，故后台获取值时需加上编码格式

		 request.setCharacterEncoding("UTF-8");
         String userName = request.getParameter("userName");




2、提交方式为get，request对象是以默认的ISO8859-1编码接收数据

		String userName = new String(request.getParameter("userName").getBytes("ISO-8859-1"),"UTF-8");
获取request对象以ISO8859-1字符编码接收到的原始数据的字节数组，然后通过字节数组以指定的编码构建字符串，解决乱码问题


3、用地址传递参数时中文参数的乱码，解决方法和get提交法式一样。

		String userName = request.getParameter("userName");
		userName =new String(userName.getBytes("ISO8859-1"), "UTF-8");
