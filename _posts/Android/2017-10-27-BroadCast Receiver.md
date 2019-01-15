---
layout: post
title:  "BroadCast Receiver"
date:   2017-10-27
desc: "BroadCast Receiver"
keywords: "android,BroadCast Receiver"
categories: [Android]
tags: [Android, BroadCast Receiver]
---

## BroadCastReceiver

Android四大组件之一， 在android系统中，广播（Broadcast）是在组件之间传播数据（Intent）的一种机制；这些组件甚至可以位于不同的进程中，这样它就像Binder机制一样，起到进程间通信的作用。本质上它就是一种组件间的通信方式，它和Binder机制不一样的地方在于，广播的发送者和接收者事先是不需要知道对方的存在的，这样带来的好处便是，系统的各个组件可以松耦合地组织在一起，这样系统就具有高度的可扩展性，容易与其它系统进行集成。

####广播的特性：

1.广播接收者的生命周期是非常短暂的，在接收到广播的时候创建，onReceive()方法结束之后销毁

2.广播接收者中不要做一些耗时的工作，否则会弹出Application No Response错误对话框

3.最好也不要在广播接收者中创建子线程做耗时的工作，因为广播接收者被销毁后进程就成为了空进程，很容易被系统杀掉

4.耗时的较长的工作最好放在服务中完成

#### Broadcast分类：

	普通广播（Normal Broadcast）

	系统广播（System Broadcast）

	有序广播（Ordered Broadcast）

	粘性广播（Sticky Broadcast）

	App应用内广播（Local Broadcast）

##### BroadcastReceiver
广播接受器，作用就是接受广播的一个组件，BroadcastReceiver是一种可以让用户接受广播通知且没有用户界面的组件，在android系统中为了响应不同的事件通知，应用程序可以注册不同的BroadcastReceiver，当BroadcastReceiver接受到通知后可以启动Activity作为响应或者通过NotificationManager提醒用户。

##### 普通广播（Normal Broadcast）

这种广播所有监听该广播的接收器都可以监听到该广播，同一个级别的接受顺序是随机的或则说是无序的，接收器不能对收到的广播做任何处理，也不能截断广播继续传播。该种类的广播用sendBroadcast发送。
		
##### 系统广播（System Broadcast）

android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广波，每个广播都有特定的Intent - Filter（包括具体的action），android常用系统广播action
	
<img src="{{'/常用的系统广播' | prepend: site.img_path}}" />
		
当使用系统广播时，只需要在注册广播接收者时定义相关的action即可，并不需要手动发送广播，当系统有相关操作时会自动进行系统广播。

##### 有序广播（Ordered Broadcast）

android中的有序广播，也是一种比较常用的广播，该种类的广播用sendOrderedBroadcast发送。

* 该中广播主要有一下特性：
	
	* 按照接收者的优先顺序来接收广播，优先级别在intent-filter中的priority中声明，-1000到1000之间，值越大优先级越高，
	
	* 可以终止广播的继续传播，接受者可以修改intent的内容。
	
	* 同级别接收顺序是随机的，级别低的后收到
	
	* 同级别动态注册高于静态注册

##### 粘性广播（Sticky Broadcast）

粘性广播的特点是Intent会一直保留到广播事件结束，而这种广播也没有所谓的10秒限制，10秒限制是指普通的广播如果onReceive方法执行时间太长，超过10秒的时候系统会将这个广播置为可以干掉的candidate，一旦系统资源不够的时候，就会干掉这个广播而让它不执行。该广播用sendStickyBroadcast发送。

##### App应用内广播（Local Broadcast）

因为android中的广播是可以跨域的（跨App），因此可能存在一下问题：

* 其他App针对性发出与当前App intent-filter相匹配的广播，由此导致当前App不断接收广播并处理；

* 其他App注册与当前App一致的intent-filter用于接收广播，获取广播具体信息； 即会出现安全性和效率性的问题。

* 对于上述情况就出现了这种广播，App应用内广播可理解为一种局部广播，广播的发送者和接收者

* 都同属于一个App。相比于全局广播（普通广播），App应用内广播优势体现在：安全性高效率高。


##### 本地广播的特点:
* 本地广播只能在本App中传播,并且广播接收器也只能接收来自本APP发出的广播.  所以在该情况下可防止其他应用获取广播中的敏感数据从而确保数据安全.

* 本地广播只能使用在代码中动态注册的方式.  因为静态注册主要是为了让程序在未启动的情况下也能收到广播.在发送本地广播时,我们的App肯定是已经启动了,因此也完全不需要使用静态注册.否则,这个本地

需要注意的是对于LocalBroadcastManager方式发送的应用内广播，只能通过LocalBroadcastManager动态注册，不能静态注册。

设置广播为App应用内广播，广播就不能跨域了，一次只能在发送广播的app（App2）内接受，而另外一个app（app test）接收不到app2发送的广播。

### 注册方式

* 静态注册
	
		<receiver android:name="com.example.unorderbroadcasereceiver.MyBroadcaseReceiver">  
			<intent-filter >  
				<action android:name="unorderbroadcast"/>  
			</intent-filter>  
		</receiver>  

 
* 动态注册
	
		myBroadcaseReceiver = new MyBroadcaseReceiver();  
		IntentFilter filter = new IntentFilter();  
		filter.addAction("unorderbroadcast");  
		registerReceiver(myBroadcaseReceiver, filter );  
		
	
* BroadCast Receiver

		public class MyBroadcaseReceiver extends BroadcastReceiver{  
			@Override  
			public void onReceive(Context context, Intent intent) {  
				// TODO Auto-generated method stub  
				Toast.makeText(context, "Normal broadcast", Toast.LENGTH_SHORT).show();  
			}  
		} 