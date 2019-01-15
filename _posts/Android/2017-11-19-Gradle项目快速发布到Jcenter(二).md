---
layout: post
title:  "Gradle项目快速发布到Jcenter(二)"
subtitle: "Gradle项目快速发布到jcenter"
date:   2017-11-19
desc: "Gradle 项目快速发布到jcenter (二)"
keywords: "android,gradle, jcenter"
categories: [Android]
catalog: true
tags: [Android, gradle]
catalog: true
---

上一篇 [Gradle项目快速发布到jcenter(一)]({{'Gradle项目快速发布到Jcenter(一).html' | prepend: site.baseurl}}) 展示了如何去jcenter创建账户，接下来就是如何，修改项目中的设置，加入jcenter的配置了。

# Gradle 项目快速发布到jcenter (二)

## 项目中设置

### root目录 

root目录下的 build.gradle中加入如下：
 
	classpath 'com.novoda:bintray-release:0.7.0'

 加入后如下：
		
	buildscript {
	    repositories {
	        jcenter()
	        google()
	    }
	    dependencies {
	        classpath 'com.android.tools.build:gradle:3.0.0'
			//jcenter插件
	        classpath 'com.novoda:bintray-release:0.7.0'
	    }
	}
	
	allprojects {
	    repositories {
	        jcenter()
	    }
	}
	
	task clean(type: Delete) {
	    delete rootProject.buildDir
	}

### app或者module

在需要发布的app或者module下的 build.gradle中加入：


	apply plugin: 'com.novoda.bintray-release'
	
	//添加,这个需要修改成你的项目信息，切记！！！！
	publish {
	    userOrg = 'bintray'//bintray.com 注册的用户名
	    groupId = 'com.easy' //jcenter上的路径
	    artifactId = 'easy'//项目名称
	    publishVersion = '1.0.4'//版本号
	    desc = 'easyutil'//描述，不重要
		//网站，不重要；尽量模拟github上的地址，例如我这样的；当然你有地址最好了
	    website = 'https://github.com/xiaowujiang/Easy'
	}
	
### 发布到jcenter

在Android studio中打开terminal, 然后输入：

	//注意替换 PbintrayKey=**** 为PbintrayKey=你的apikey
	//PbintrayUser=*** 替换为PbintrayUser=你的用户名
 	gradlew clean build bintrayUpload -PbintrayUser=*** -PbintrayKey=****** -PdryRun=false

等待编译，出现SUCCESS,就说明ok了。

### 注意事项

#### 版本更新

我们上传完成后，肯定会涉及到更新问题，那么怎么更新呢？

	//添加,这个需要修改成你的项目信息，切记！！！！
	publish {
	    userOrg = 'bintray'//bintray.com 注册的用户名
	    groupId = 'com.easy' //jcenter上的路径
	    artifactId = 'easy'//项目名称
	    publishVersion = '1.0.5'//版本号
	    desc = 'easyutil'//描述，不重要
	    //网站，不重要；尽量模拟github上的地址，例如我这样的；当然你有地址最好了
	    website = 'https://github.com/xiaowujiang/Easy'
	}

什么都不用动，唯独修改下版本号就可以了，修改完成，执行上传那段代码。

#### 乱码问题

注释中有中文，可能会出现：编码GBK的不可映射字符

项目的build.gradle（根目录下的build.gradle）中添加：

	 allprojects {
	    tasks.withType(Javadoc) {
	        options{
	            encoding "UTF-8"
	            charSet 'UTF-8'
	            links "http://docs.oracle.com/javase/7/docs/api"
	        }
	    }
	}

利用bintray-release上传开源项目到jcenter就over。


## 添加审核

在jcenter中，我们找到maven仓库，点击进入找到你的项目：

<img src="{{ 'jcenter下的包.png' | prepend: site.img_path | prepend: site.baseurl}} "/>

点击进入项目中，然后点击add加入commit就行了，一般需要等待审核通过，一般5-6小时

<img src="{{'jcenter-加入审核.png' | prepend: site.img_path | prepend: site.baseurl}}"/>

## 依赖使用

在审核通过前可以通过配置自己私有仓地址使用

<img src="{{'jcenter依赖使用.png' | prepend: site.img_path | prepend: site.baseurl}}"/>

在更目录下build.gradle配置地址

	allprojects {
	    repositories {
	        jcenter()
	        maven{url 'https://dl.bintray.com/xiaowujiang/maven/'}
	    }
	    ...
	}

在项目中引用

	compile 'cn.xwj:easy:1.1.5'