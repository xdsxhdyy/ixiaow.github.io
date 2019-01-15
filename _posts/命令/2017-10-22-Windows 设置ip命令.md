---
layout: post
title:  "Windows常用命令"
date:   2017-10-22
desc: "Windows常用命令"
keywords: "Windows,命令"
categories: [Cmd]
tags: [Windows, 命令]
---

### 设置指定的ip

* 设置ip
            
        netsh interface ip set address name ="本地连接" source= static 192.168.0.5 255.255.255.0 192.168.0.1
    
* 设置dns
    
        netsh interface ip set dnsservers name ="本地连接" source= static  addr = 8.8.8.8
             
             
### 设置自动获取ip

* 设置ip
    
        netsh interface ip set address name="本地连接" source= dhcp
            
* 设置dns 
    
        netsh interface ip set dnsservers name="本地连接" source=dhcp
             
    
    
    