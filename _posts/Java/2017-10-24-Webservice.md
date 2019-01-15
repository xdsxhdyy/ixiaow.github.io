---
layout: post
title:  "webservice"
date:   2017-10-24
desc: "webservice"
keywords: "java,webservice"
categories: [Java]
tags: [java, webservice]
---

JAXWS几乎适合所有Webservice客户端的调用，它是java自带的轻量级框架，它出现在jdk1.6中，为我们开发webservice带来了便利。

### 服务端

### 1. 编写webservice 方法需要的注解

* 注解

	@WebService（类）

		name  接口名【接口】
		serviceName  服务名【实现】
		endpointInterface  声明实现类对应的接口类【实现】
		targetNamespace 自定义命名空间【接口和实现类必须统一】
		portName  端口标签的名称
		
	
	@WebMethod（方法）
	
	    exclude  排除该方法（不发布该方法）
	    operationName  发布的方法“别名”
	
	@WebParam（形参 name  定义发布的参数名称）
		
	---
	
	@SOAPBinding(style=Style.RPC, use=Use.LITERAL)
		
	*  style 有两种【RPC,DOCUMENT】
		
		* RPC ---> 是以方法调用的方式描述WebSerivce的 【说白了就是将方法名，参数，返回值 完全曝光给客户端，客户端根据这些信息进行调用方法】
		
		* DOCUMENT ---> Document则是以文档传输的方式描述WebService【只提供方法名，不提供具体的参数】

	* use 有两种【LITERAL, ENCODED】
		
		* LITERAL 文字方式
		* ENCODED 编码方式
 
	* 组合方式
		
		* 常用 DOCUMENT/LITERAL
		
		* 具体请查看 https://www.ibm.com/developerworks/cn/webservices/ws-whichwsdl/#listing9


### 2. 实现WebService

* jar 包【官网提供】----

	* gmbal-api-only.jar
	* ha-api.jar
	* jaxb-impl.jar
	* jaxws-api.jar
	* jaxws-rt.jar
	* management-api.jar
	* policy.jar
	* stax-ex.jar
	* streambuffer.jar

* 创建一个接口

		@WebService(targetNamespace ="http://webservice.um.com/")

		@SOAPBinding(style = Style.RPC, use = Use.LITERAL)

		public interface BindInfoWebService {
			@WebMethod
			public String sayHello(@WebParam(name="name")String name);
		}

* 接口实现类

		@WebService(endpointInterface="com.um.webservice.BindInfoWebService")
	
		public class BindInfoWebServiceImpl implements BindInfoWebService{
			@Override
			public String sayHello(String name) {
				System.out.println("Hello " + name);
				return "Hello"+ name;
			}
		}

### 3.发布webService

##### 方式一： 利用java方式

	public class Publish {
		public static void main(String[] args) {
			//自定义的URL
			String address = "http://192.168.1.3:9897/cal";
			//创建WebService类的实例
			Object implementor = new CalculatorImpl();
			//发布
			Endpoint.publish(address, implementor);
			System.out.println("计算服务发布成功。。。");
		}
	}

##### 方式二： 利用tomcat等服务器
	
1. 编写 sun-jaxws.xml
	
		<?xml version="1.0" encoding="UTF-8"?>
		<endpoints xmlns="http://java.sun.com/xml/ns/jax-ws/ri/runtime"
			version="2.0">
			<endpoint name="BindInfoWebService" implementation="com.um.webservice.BindInfoWebServiceImpl"
				url-pattern="/service/sayHi" />
		</endpoints>

2. 配置Web.xml
			
	* 配置一个Listener 
		
			<listener>  
				<listener-class>
					<!--如果没有实现WSServletContextListener 默认配置 -->
					com.sun.xml.ws.transport.http.servlet.WSServletContextListener  
				</listener-class>
			</listener>

	* 配置一个Servlet 
	
			<servlet>
				<servlet-name>SayHiService</servlet-name>  
				<!--如果没有实现WSServletContextListener 默认配置 -->;
				<servlet-class>
					com.sun.xml.ws.transport.http.servlet.WSServlet  
				</servlet-class>
				<load-on-startup>1</load-on-startup>
			</servlet>  
			<servlet-mapping>  
				<servlet-name>SayHiService</servlet-name>  
				<url-pattern>/service/sayHi</url-pattern>  
			</servlet-mapping>

##客户端

### 工具生成客户端[可生成java代码的客户端]

	wsimport -keep http://localhost:8080/helloworld/HelloWorldService?wsdl

### android 利用KSoap-Android 编写





