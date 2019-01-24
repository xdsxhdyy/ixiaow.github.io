---
layout: post
title:  "Apache cxf实现WebServices--服务端"
date:   2017-11-29
desc: "Apache cxf实现WebServices--服务端"
keywords: "java web,cxf"
categories: [javaweb]
tags: [java web, WebServices, cxf]
---


# Apache cxf实现WebServices--服务端

### 1.WebServices及cxf简单介绍

   Web Service就是部署在Web服务器上的一个，向外界暴露出一个能够通过Web进行调用的API。Web Service 最大的优势是提供了异构平台的无缝街接技术手段，使任何两个应用程序，只要能读写XML，就能实现互相通信。

   Apache CXF 是一个开源的 Services 框架，CXF构建和开发 Services 这些 Services 可以支持多种协议，比如：SOAP、POST/HTTP、RESTful HTTP ，CXF大大简化了 Service可以天然地和 Spring进行无缝集成。CXF与Spring集成发布WebService。

### 2.配置文件

web.xml 里面配置cxf的servlet

	  <!-- <servlet>元素用于注册Servlet，它包含有两个主要的子元素：
	  <servlet-name>和<servlet-class>，分别用于设置Servlet的注册名称和Servlet的完整类名。  -->
	  
	  <servlet>
	    <servlet-name>CXFServlet</servlet-name>
	    <servlet-class>org.apache.cxf.transport.servlet.CXFServlet</servlet-class>
	    <init-param>
	      <param-name>contextConfigLocation</param-name>
	      <param-value>classpath:cxf-servlet.xml</param-value>
	    </init-param>
	    <load-on-startup>1</load-on-startup>
	  </servlet>
	  
	  <!-- <servlet-mapping>元素用于映射一个已注册的Servlet的一个对外访问路径，它包含有两个子元素：
	  <servlet-name>和<url-pattern>，分别用于指定Servlet的注册名称和Servlet的对外访问路径 -->
	  
	  <servlet-mapping>
	    <servlet-name>CXFServlet</servlet-name>
	    <url-pattern>/services/*</url-pattern>
	  </servlet-mapping>

cxf-servlet.xml

	<?xml version="1.0" encoding="UTF-8"?>  
	  
	<beans xmlns="http://www.springframework.org/schema/beans"  
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
	    xmlns:jaxws="http://cxf.apache.org/jaxws"  
	    xsi:schemaLocation="  
	        http://www.springframework.org/schema/beans 
	        http://www.springframework.org/schema/beans/spring-beans.xsd  
	        http://cxf.apache.org/jaxws 
	        http://cxf.apache.org/schemas/jaxws.xsd">  
	          
	    <import resource="classpath:META-INF/cxf/cxf.xml" />  
	    <import resource="classpath:META-INF/cxf/cxf-servlet.xml" />  
	  
	  <!-- id:逻辑名  serviceClass：服务接口类  address:调用的路径
	    **http://localhost:8081/scm_server/services/testService?wsdl**> -->
	  <jaxws:server id="testService" serviceClass=" test.ws.test.ITestService" address="/testService">  
	    <jaxws:serviceBean>  
	        <bean class=" test.ws.test.TestServiceImpl" />  
	    </jaxws:serviceBean>  
	  </jaxws:server>  
	  
	</beans>

### 3.服务端发布webservices接口

	//接口
	@WebService
	public interface ITestService {
		public String sayHi(String name);
	}

	//实现
	@WebService(endpointInterface="test.ws.test.ITestService") //设置服务端点接口，对外提供服务的接口
	public class TestServiceImpl implements ITestService {
		@Autowired
		IUserService userService;
		
		public String sayHi(String name) {
			return name+", welcome to here!";
	}


### 4.访问wsdl（Web服务描述语言）

通过XML形式说明服务在什么地方－地址，
说明服务提供什么样的方法 – 如何调用。

路径：http://localhost:8081/scm_server/services/testService?wsdl

	该 XML 文件并未包含任何关联的样式信息。文档树显示如下。
	      <wsdl:definitions name="ITestServiceService" targetNamespace="http://test.ws.test/"><wsdl:types><xs:schema elementFormDefault="unqualified" targetNamespace="http://test.ws.test/" version="1.0"><xs:element name="sayHi" type="tns:sayHi"/><xs:element name="sayHiResponse" type="tns:sayHiResponse"/><xs:complexType name="sayHi"><xs:sequence><xs:element minOccurs="0" name="arg0" type="xs:string"/></xs:sequence></xs:complexType><xs:complexType name="sayHiResponse"><xs:sequence><xs:element minOccurs="0" name="return" type="xs:string"/></xs:sequence></xs:complexType></xs:schema></wsdl:types><wsdl:message name="sayHi"><wsdl:part element="tns:sayHi" name="parameters">
	    </wsdl:part></wsdl:message><wsdl:message name="sayHiResponse"><wsdl:part element="tns:sayHiResponse" name="parameters">
	    </wsdl:part></wsdl:message><wsdl:portType name="ITestService"><wsdl:operation name="sayHi"><wsdl:input message="tns:sayHi" name="sayHi">
	    </wsdl:input><wsdl:output message="tns:sayHiResponse" name="sayHiResponse">
	    </wsdl:output></wsdl:operation></wsdl:portType><wsdl:binding name="ITestServiceServiceSoapBinding" type="tns:ITestService"><soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/><wsdl:operation name="sayHi"><soap:operation soapAction="" style="document"/><wsdl:input name="sayHi"><soap:body use="literal"/></wsdl:input><wsdl:output name="sayHiResponse"><soap:body use="literal"/></wsdl:output></wsdl:operation></wsdl:binding><wsdl:service name="ITestServiceService"><wsdl:port binding="tns:ITestServiceServiceSoapBinding" name="ITestServicePort"><soap:address location="http://localhost:8081/scm_server/services/testService"/></wsdl:port></wsdl:service></wsdl:definitions>


本来想截图的，但是编辑器有问题，只能复制wsdl的代码了，看着有点乱，可以自己写一下按路径访问就有了，OK就这样。

相关jar包下载：http://download.csdn.net/download/xdsxhdyy/10136638


