---
layout: post
title:  "Android获取IP"
date:   2017-10-25
desc: "Android获取ip"
keywords: "android,ip"
categories: [Android]
tags: [Android, ip]
---


## android 获取ip

### 绝对可行的一种

##### 先判断是否有网络

	public static boolean checkEnable(Context ctx) {
		ConnectivityManager cm = (ConnectivityManager) ctx.getSystemService(Context.CONNECTIVITY_SERVICE);
		NetworkInfo ni = cm.getActiveNetworkInfo();
		return ni != null && ni.isAvailable();
	}

##### 再获取

	public static String getLocalIpAddress() {
		try {
			Enumeration<NetworkInterface> interfaces = NetworkInterface.getNetworkInterfaces();
			//遍历所有的网络接口
			while (interfaces.hasMoreElements()) {
				NetworkInterface element = interfaces.nextElement();
				Enumeration<InetAddress> addresses = element.getInetAddresses();
				//遍历每一个网口所绑定的所有ip
				while (addresses.hasMoreElements()) {
					InetAddress inetAddress = addresses.nextElement();
					if (!inetAddress.isLoopbackAddress() && inetAddress instanceof Inet4Address) {
						String ip = inetAddress.getHostAddress();
						if (!"127.0.0.1".equals(ip)) {
							return ip;
						}
					}
				}
			}
		} catch (SocketException e) {
			e.printStackTrace();
		}
		return null;
	}
	
