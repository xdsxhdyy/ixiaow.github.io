---
layout: post
title:  "Java调用本地软件打开文件"
date:   2017-10-22
desc: "Java调用本地软件打开文件"
keywords: "java,打开本地文件"
categories: [Java]
tags: [java, 本地文件]
---

## 调用本地软件打开文件

    Runtime runtime = Runtime.getRuntime();  
    try{  
        System.out.println(f.getAbsolutePath());  
        //打开文件  
        runtime.exec("rundll32 url.dll FileProtocolHandler "+f.getAbsolutePath());  
    }catch(Exception ex){  
        ex.printStackTrace();  
    }  
