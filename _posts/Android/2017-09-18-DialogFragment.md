---
layout: post
title:  "DialogFragment创建对话框"
date:   2017-09-18
desc: "Android DialogFragment创建对话框"
keywords: "android,DialogFragment,对话框"
categories: [Android]
tags: [Android, DialogFragment, 对话框]
---
## Android官方推荐： DialogFragment创建对话框

DialogFragment在android 3.0时被引入。是一种特殊的Fragment，用于在Activity的内容之上展示一个模态的对话框。典型的用于：展示警告框，输入框，确认框等等。

在DialogFragment产生之前，我们创建对话框：一般采用AlertDialog。

* 好处与用法

	使用DialogFragment来管理对话框，当旋转屏幕和按下后退键时可以更好的管理其生命周期，它和
	Fragment有着基本一致的生命周期。且DialogFragment也允许开发者把Dialog作为内嵌的组件进行重用，类似Fragment（可以在大屏幕和小屏幕显示出不同的效果）。
	
	使用DialogFragment至少需要实现onCreateView或者onCreateDialog方法。onCreateView即使用定义的xml布局文件展示Dialog。onCreateDialog即利用AlertDialog创建出Dialog。

*  重写onCreateView创建Dialog

	*  布局文件，我们创建一个设置名称的布局文件
	*  继承DialogFragment，重写onCreateView方法

* 重写onCreateDialog创建Dialog
	
	* 使用AlertDialog来创建一个对话框
	* 继承DialogFragment重写onCreateDialog方法
	
* 传递数据给Activity

	* 从dialog传递数据给Activity，可以使用“fragment interface pattern”的方式

* DialogFragment做屏幕适配
	
	* 一个对话框在大屏幕上以对话框的形式展示，而小屏幕上则直接嵌入当前的Actvity中。这种效果的对话框，只能通过重写onCreateView实现。
	
* 屏幕旋转

	* 传统的new AlertDialog在屏幕旋转时，第一不会保存用户输入的值，第二还会报异常，因为Activity销毁前不允许对话框未关闭。而通过DialogFragment实现的对话框则可以完全不必考虑旋转的问题。 