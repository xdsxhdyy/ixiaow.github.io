---
layout: post
title:  "WebService 注解"
date:   2017-09-17
desc: "WebService 注解"
keywords: "java web"
categories: [javaweb]
tags: [java web, 注解]
---

# WebService 注解

* 说明：
	
	1.WSDL文件的内容，一般由服务默认生成，是服务的使用说明书，一般应做一些简单的修改。至少不应该暴露我们的包结构。而targetNamespace默认情况下为倒置的包名，这已经暴露了我们的包结构。

	2.通过在类文件上添加以下注解，可以修改wsdl生成的各元素，而不是直接去修改wsdl文件，直接去修改wsdl文件是无效的。

	WebService的注解包括：

		@WebService-定义服务   --类上
		@WebMethod-定义方法   － 方法
		@WebResult-定义返回值 – 返回值 
		@WebParam-定义参数 – 参数

* 注解的好处：

	1. 通过WebService的注解，可以更加形像的描述Web服务。从而生成WSDL文档。
	2. 当修改了WebService注解之后，同时会影响客户端生成的代码；调用的方法名和参数名也发生了变化。
	3. 即使是没有修改源代码，只修改了注解，客户端的代码也必须要重新生成(注意是生成而不是下载)。否则调用将会失败。



* 逐个讲解：

	1. @WebService注解：
				
			@WebService注解，作用在具体类上。而不是接口。
			一个类只有添加了此注解才可以通过Endpoint发布为一个web服务。
			一个添加了此注解的类，必须要至少包含一个实例方法。静态方法和final方法不能被发布为服务方法。
			WebService注解包含以下参数：
			1.String endpointInterface  --服务端点接口的名称
			2.String name  -- webservice的名称
			3.String portName  -- webservice的端口名称
			4.String serviceName  --webservice的服务名称
			5.String targetNamespace  --命名空间
			6.String wsdlLocation  --服务的预定义wsdl位置

	2. @WebMethod：
			
			此注解用在方法上，用于修改对外暴露的方法。
			1.String action  --此操作的动作
			2.boolean exclude  --将方法标记为不作为web方法公开
			3.String operationName  --与此方法匹配的wsdl:operation的名称

	3. @WebResult：
	
			@WebResult用于定制返回值到WSDL的映射
			1.String name  --返回值的名称
			2.String partName  --表示此返回值的wsdl:part的名称
			3.String targetNamespace  --返回值的XML名称空间

	4. @WebParam：
	
			@WebParam用于定制返回值到WSDL的映射
			1.WebParam.Mode mode  --参数的流向（IN,OUT或INOUT之一）
			2.String name  --参数的名称
			3.String partName  --表示参数的wsdl:part的名称
			4.String targetNamespace  --参数的XML名称空间


* 代码展示：

		@WebService (serviceName = "PhoneManages", targetNamespace = "http://get.phone.info.cn")
		//修改服务名 和命名空间（默认是包名取反）
		public class PhoneService {
		/**
		 * @WebResult(name = "phone") 修改返回参数名
		 * @WebParam(name = "osName") 修改输入参数名
		 * 
		 */
			@WebMethod (operationName = "getMobileInfo") //修改方法名
			public @WebResult(name = "phone") Phone getPhoneInfo(@WebParam(name = "osName") String osName){  //返回值名phone，输入参数osName
				Phone phone = new Phone();
				System.out.println(osName);
				return phone;
			}
			
			/**
			 * @param args
			 */
			public static void main(String[] args) {
				String address = "http://192.168.135.34:8888/PhoneService";
				Endpoint.publish(address, new PhoneService());
			}
	
		}
