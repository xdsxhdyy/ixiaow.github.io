---
layout: post
title:  "ThreadLocal 模式"
date:   2017-10-24
desc: "ThreadLocal 模式"
keywords: "java,ThreadLocal 模式"
categories: [Java]
tags: [java, ThreadLocal 模式]
---

#ThreadLocal 模式

* ThreadLocal 维护线程的局部变量

* ThreadLocal 不是一个线程。它就是一个map，可以用来保存对象

* ThreadLocal 保存的对象只与当前线程有关

* 当一个线程还没有运行完成时，如果不想传递数据，可以通过ThreadLocal来保存与这个Thread相关的数据。

###用ThreadLocal 保存和获取数据

	public static void main(String[] args){
		//声明 Map<Object key, Object value>
		// value 是值    key 是 Thread.currnetThread()
		ThreadLocal<Object> tl = new ThreadLocal<Object>();
		//设置数据
		tl.set("Hello");
		//获取数据
		Object o = tl.get()
	}

### 

当多个线程共同访问同一个资源时，用threadLocal来维护某个线程的变量：

一个应用项目中，一般只要有一个（static）threadlocal的实例就可以了：
	
	public class MyThreadLocal {
		//声明一个唯一的ThreadLocal
		private static ThreadLocal<Object> tl = new ThreadLocal<Object>();[声明线程局部的容器对象。]
		public static Object getObject(){
			//先从tl中读取数据
			Object o = tl.get();//[先从tl中获取与当前线程相关的对象。] 如果没有保存过，map.get(Thread.currentThread());
			if(o==null){
				//生成一个随机
				o = new Random().nextInt(100);
				//放到tl
				tl.set(o);[如果没有就声明一个新的放到tl中。]
			}
			return o;
		}
		public static void remove(){
			tl.remove();[可选的删除这个对象。]
		}
	}
对ThreadLocal内部保存的对象来说。你可以执行remove(无数)方法删除与当前thread相关的对象。也可以不执行：
因为：threadlocal内部使用的是弱引用：
WeakReferences

在java当对象，有四种引用方式：
强
软
弱
虚
