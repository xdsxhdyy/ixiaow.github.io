---
layout: post
title:  "Android Studio"
date:   2017-09-20
desc: "Android Studio的快捷键和一些错误"
keywords: "android,android studio"
categories: [Android]
tags: [Android, Android Studio]
---


## Andorid Studio 快捷键
	
	Alt + 回车 ----->自动修复
	Alt + insert  ---> 添加get、set等方法

	Ctrl + P ----> 提示
	Ctrl + ALT+空格 自动提示，类似于eclipse中的alt+/
	Ctrl + d --->复制当前行
	Ctrl + y----> 删除当前行
	Ctrl + Alt + o --->优化导包
	
	Ctrl + / ---> 当行注释，反注释再按一次即可
	Ctrl + Shift + / ---> 模块注释，反注释再按一次即可
	Ctrl + Alt + V ---> 生成方法返回值

	sout 打印
	f8 调试

	Ctrl + E 显示最近编辑的文件列表
	Ctrl + N 快速打开类
	Ctrl + Shift + N 快速打开文件
	Ctrl + B 查看当前方法所在的类

## Android Studio设置

1. Code Style
	
	Java---->一般选择默认样式
	
2. Build,Exexution,Deployment

	    complier--> make project a automatically  自动编译
	    Gradle--> Gradle home 设置打包路径  Offine work 勾选后不会每次从网上下载
	
3. File Encoding

	    统一编码
	
4. Tools 
	    
		Task--> Connection timeout 更改模拟器链接超时时间
	    Terminal-->cmd 控制命令
	
5. Apperance

	    勾选Override default fonts 设置字体样式 
	
6. Editor
	       
		勾选show quick doc ... 快速提示
	    apprence勾选show right margin...右边友好提示线
	
7. Code Completion 

	    Case sensitive 改为none 单词匹配是否区分大小写

	    Auto Import 勾选 add unambiguous imports on the fly 自动导包
	
8. Project Struct.. 

	    SDK选择路径 JDK jdk路径
	
9. 设置成员变量前面是m, 静态变量是s

	Settings ---> Editor ---> Code Style ---> Java ---> Code Generation 
    
	<img src="{{'/Android studio.png' | prepend: site.img_path}}" width="50%">
	
## Android Studio Gradle乱码

* 在当前build.grade中添加：

		tasks.withType(JavaCompile) { 
			options.encoding = "UTF-8"
		}