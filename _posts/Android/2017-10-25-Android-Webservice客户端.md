---
layout: post
title:  "Android WebService客户端"
date:   2017-10-25
desc: "Android WebService客户端"
keywords: "android,webservice, Ksoap"
categories: [Android]
tags: [Android, WebService, Ksoap]
---
	
##android webserivice 调用方式

### 利用KSoap android.jar

	public  String callMethod(String methodName, Map<String, ?extends Object> infos) throws Exception {

		String namespace = "http://service.um.com/";
		String serviceUrl = "http://192.168.1.3:8080/Service/SayHello?wsdl";
		KSoapHelper helper = new KSoapHelper(namespace, serviceUrl);

		// 获取的request
		SoapObject request = helper.getRequest(methodName, infos);

		// 获取envelope
		SoapSerializationEnvelope envelope = helper.getSoapSerializationEnvelope(request);

		// 设置transport
		HttpTransportSE transport = helper.getHttpTransportSE();

		// 调用方法
		helper.callMethod(transport, envelope);

		// 获取回调信息
		SoapPrimitive soapPrimitive = helper.getSoapPrimitive(envelope);

		String value = (String) soapPrimitive.getValue();
		System.out.println(value);
		return value;

	}

其中 KsoapHelper为根据KSoap-android.jar 自己编写


	public class KSoapHelper {

		private String namespace = "";
		private String serviceUrl = "";
		private String soapAction = "";

		public KSoapHelper(String namespace, String serviceUrl) {
			this.namespace = namespace;
			this.serviceUrl = serviceUrl;
		}

		public SoapObject getRequest(String nameSpace, String methodName, Map<String, ?extends Object> params) {

			// 创建SoapObject request
			SoapObject request = new SoapObject(nameSpace, methodName);
			this.soapAction = getSoapAction(methodName);
			if (params != null && params.size() > 0) {
				for (Entry<String, ?> param : params.entrySet()) {
					PropertyInfo prop = new PropertyInfo();
					prop.setName(param.getKey());
					prop.setValue(param.getValue());
					prop.setType(prop.getValue().getClass());
					request.addProperty(prop);
				}
			}
			return request;
		}

		public SoapObject getRequest(String methodName, Map<String,?extends Object > params) {

			return getRequest(this.namespace, methodName, params);
		}

		// Creating an Envelope
		public SoapSerializationEnvelope getSoapSerializationEnvelope(SoapObject request) {
			SoapSerializationEnvelope envelope = new SoapSerializationEnvelope(SoapEnvelope.VER11);
			envelope.dotNet = false;// .net框架设置为true
			envelope.implicitTypes = true;
			envelope.setAddAdornments(false);
			envelope.setOutputSoapObject(request);
			return envelope;
		}

		public HttpTransportSE getHttpTransportSE(String serviceUrl) {
			HttpTransportSE ht = new HttpTransportSE(Proxy.NO_PROXY, serviceUrl, 60000);
			ht.debug = true;
			ht.setXmlVersionTag("<!--?xml version=\"1.0\" encoding= \"UTF-8\" ?-->");
			return ht;
		}

		public HttpTransportSE getHttpTransportSE() {
			return getHttpTransportSE(this.serviceUrl);
		}

		// Sending the Request
		public void callMethod(HttpTransportSE ht, SoapSerializationEnvelope envelope) throws Exception{
			ht.call(this.soapAction, envelope);
		}

		// Processing the Response
		public SoapPrimitive getSoapPrimitive(SoapSerializationEnvelope envelope) throws SoapFault {
			return (SoapPrimitive) envelope.getResponse();
		}

		public String getSoapAction(String methodname) {
			return this.namespace + (this.namespace.endsWith("/") ? "" : "/") + methodname;
		}
	}
