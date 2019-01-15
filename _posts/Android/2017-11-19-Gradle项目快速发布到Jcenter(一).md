---
layout: post
title:  "Gradle项目快速发布到Jcenter(一)"
subtitle: "Gradle 项目快速发布到jcenter"
date:   2017-11-19
desc: "Gradle 项目快速发布到jcenter (一)"
keywords: "android,gradle, jcenter"
categories: [Android]
catalog: true
tags: [Android, gradle]
catalog: true
---

# Gradle 项目快速发布到jcenter (一)

最近编写了一个android的Utils集合库 **[Easy](https://github.com/xiaowujiang/Easy)**，使用过Android Studio或者Intellij idea IDE的都知道，我们可以在Root目录下的build.gradle文件中添加下面这句，就可以把一个库（module）引入到项目中来。

	compile 'cn.xwj:easy:1.0.0'

于是自己在参考了大神们的文章后，自己也去实践了一把，将过程分享出来。

## 创建Jecenter账户

### 网址

* [https://bintray.com/](https://bintray.com/)

	<img src="{{'jcenter-home.jpg'| prepend: site.img_path | prepend: site.baseurl}}"/>

### 注册 | 登录

* 注册（有账号的不需要注册这部分）： 进入网址后，一定要选择 **<font color='red'> “For an Open Source Account Sign up here ”</font>**

	注册时可以用GitHub、Google和 Twitter帐号快捷登录（这些都需要翻墙才可以），
	也可以用输入用户名密码等信息注册，但是邮箱不能用QQ、163国内的，用微软的outlook或者google的gmail。126没有试过。

	<img src="{{'jcneter-login.png' | prepend: site.img_path | prepend: site.baseurl}}" />


### 创建Maven仓库

* 注册成功后，进入用户页，**第一次注册成功**后如果我们的帐户里是没有Maven仓库，那么需要先创建一个（有的话就不用创建），名字`maven`, 类型`maven`， 其余的看着选或者填


	<img src="{{ 'jcenter-create maven.png' | prepend: site.img_path | prepend: site.baseurl}}" />


### ApiKey

* Edit Profile

	进入主页后，点击右上角的头像，选择 `edit profile`

	<img src="{{'查看apikey-1.png' | prepend: site.img_path | prepend: site.baseurl }}" />
	
* 查看ApiKey

	<img src="{{ '查看apikey-2.png' | prepend: site.img_path | prepend: site.baseurl }}" />

* 记录apikey

	将你的apikey记录下来，会有大作用（欲知作用如何，请往下看），记住apikey一定要保密

	<img src="{{ '查看apikey-3.png' | prepend: site.img_path | prepend: site.baseurl}}" />

## 项目设置

**有关项目设置请看[Gradle项目发布到Jcenter(二)]({{'Gradle项目快速发布到Jcenter(二).html' | prepend: site.baseurl }})**