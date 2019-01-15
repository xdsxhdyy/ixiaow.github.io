---
layout: post
title:  "Android Service"
date:   2017-09-02
desc: "Android Service"
keywords: "android,service"
catalog: true
categories: [Android]
tags: [Android, Service]
---


Service是Android的四大组件之一，使用Service可以在后台执行长时间的操作，Service并不与用户产生UI交互。其他的应用组件可以启动Service，即便用户切换了其他应用，启动的Service仍可在后台运行。一个组件可以与Service绑定并与之交互，甚至是跨进程通信（IPC）。

**注意：** Service运行在`主线程`中，并不是一个新的线程，也不是新的进程。因此，在Service中执行较为耗时的操作，需要在Service中创建一个新的线程。这可以防止ANR的发生，同时主线程可以执行正常的UI操作。

## 使用Service还是使用Thread

Service是一个运行在后台的组件，并不与用户交互。
当用户正在与UI交互时，需要执行一些主线程无法完成的工作，应当创建一个线程。例如当activity正在运行时，需要播放音乐，此时需要在Activity的onCreate()中创建线程并在onStart()中开启。最后在onStop()中停止。您也可以考虑使用AsyncTask 或 HandlerThread来替代Thread创建线程。

## Service的生命周期

为了创建Service，需要继承Service类。并重写它的回调方法，这些回调方法反应了Service的生命周期，并提供了绑定Service的机制。

 
<image src="{{'/service_lifecycle.png' | prepend: site.img_path }}" />


* onStartCommand()：当其他组件调用startService()方法请求启动Service时，该方法被回调。一旦Service启动，它会在后台独立运行。当Service执行完以后，需调用stopSelf() 或 stopService()方法停止Service。（若您只希望bind Service，则无需调用这些方法）

* onBind()：当其他组件调用bindService()方法请求绑定Service时，该方法被回调。该方法返回一个IBinder接口，该接口是Service与绑定的组件进行交互的桥梁。若Service未绑定其他组件，该方法应返回null。

* onCreate()：当Service第一次创建时，回调该方法。该方法只被回调一次，并在onStartCommand() 或 onBind()方法被回调之前执行。若Service处于运行状态，该方法不会回调。

* onDestroy()：当Service被销毁时回调，在该方法中应清除一些占用的资源，如停止线程、接触绑定注册的监听器或broadcast receiver 等。该方法是Service中的最后一个回调。

* onUnbind(): 当Service被解绑时调用

* onRebind()：当service被重新绑定时调用

	
**注：** 如果某个组件通过调用bindService()绑定了Service，只要该组件与Service处于绑定状态，Service就会一直运行，当Service不再与组件绑定时，该Service将被destroy。

* 在两条生命周期路径中，都包含了两个嵌套的生命周期：

	完整生命周期：从onCreate()被调用到onDestroy()返回。与Activity类似，一般在onCreate()中做一些初始化工作，而在onDestroy()做一些资源释放工作
	无论是startService() 还是 bindService()启动Service，onCreate() 和 onDestroy()均会被回调。
	
	活动生命周期（active lifetime）：从onStartCommand() 或 onBind()回调开始。由相应的startService() 或 bindService()调用。 
	若是Start Service，那么Service的活动生命周期结束就意味着其完整生命周期结束 （the active lifetime ends the same time that the entire lifetime ends），即便onStartCommand()返回后，Service仍处于活动状态；若是bind service，那么当onUnbind()返回时，Service的活动生命周期结束。 

**注意：** 针对Start Service，由于Service中没有类似onStop()的回调，所以在调用stopSelf() 或 stopService()后，只有onDestroy()被回调标志着Service已停止。

* 在manifest文件中注册service

		<manifest ... >
		  ...
		  <application ... >
		      <service android:name=".ExampleService" />
		      ...
		  </application>
		</manifest>

	除此之外，在<service>标签中还可以配置其他属性，比如，需要启动该service所需的权限、该service应运行在哪个进程中。android:name属性是唯一不可缺省的，它指定了Service的全限定类名。一旦发布了应用，该类名将不可更改。
	
	将android:exported属性设为false，表示不允许其他应用程序启动本应用的组件，即便是显式Intent也不行。这可以防止其他应用程序启动您的service组件。

**注意：** 为了保证应用的安全，请使用显式Intent启动或绑定一个Service，请不要在<service>标签中配置intent-filter。


* 继承Service类：请务必在Service中开启线程来执行耗时操作，因为Service运行在主线程中。
	在大多数情况下，start Service并不会同时处理多个请求，因为处理多线程较为危险，所以继承IntentService类带创建Service是个不错选择。

	如果您需要在Service中执行多线程而不是处理一个请求队列，那么需要继承Service类，分别处理每个Intent。在Service中执行操作时，处理每个请求都需要开启一个线程，并且同一时刻一个线程只能处理一个请求。

## IntentService	

* 继承IntentService类：IntentService继承于Service，若Service不需要同时处理多个请求，那么使用IntentService将是最好选择：您只需要重写onHandleIntent()方法，该方法接收一个回传的Intent参数，您可以在方法内进行耗时操作，因为它默认开启了一个子线程，操作执行完成后也无需手动调用stopSelf()方法，onHandleIntent()会自动调用该方法，特点如下：
	
	* 默认在子线程中处理回传到onStartCommand()方法中的Intent；

	* 在重写的onHandleIntent()方法中处理按时间排序的Intent队列，所以不用担心多线程带来的问题。

	* 当所有请求处理完成后，自动停止service，无需手动调用stopSelf()方法；

	* 默认实现了onBind()方法，并返回null；

	* 默认实现了onStartCommand()方法，并将回传的Intent以序列的形式发送给onHandleIntent()，您只需重写该方法并处理Intent即可。
	
	综上所述，您只需重写onHandleIntent()方法即可，当然，还需要创建一个构造方法，示例如下：
	
		public class HelloIntentService extends IntentService {
			public HelloIntentService() {
				super("HelloIntentService");
			}
			@Override
			protected void onHandleIntent(Intent intent) {
			}
		}

	如果您还希望在IntentService的 继承类中重写其他生命周期方法，如onCreate()、onStartCommand() 或 onDestroy()，那么请先调用各自的父类方法以保证子线程能够正常启动。
	
	比如，要实现onStartCommand()方法，需返回其父类方法：
	
		@Override
		public int onStartCommand(Intent intent, int flags, int startId) {
		    Toast.makeText(this, "service starting", Toast.LENGTH_SHORT).show();
		    return super.onStartCommand(intent,flags,startId);
		}
	
	除onHandleIntent()外，onBind()方法也无需调用其父类方法。
	

* 注意到onStartCommand()返回一个整型变量，该变量必须是下列常量之一：

	* START_NOT_STICKY：若执行完onStartCommand()方法后，系统就kill了service，不要再重新创建service，除非系统回传了一个pending intent。这避免了在不必要的时候运行service，您的应用也可以restart任何未完成的操作。
	
	* START_STICKY：若系统在onStartCommand()执行并返回后kill了service，那么service会被recreate并回调onStartCommand()。dangerous不要重新传递最后一个Intent（do not redeliver the last intent）。相反，系统回调onStartCommand()时回传一个空的Intent，除非有 pending intents传递，否则Intent将为null。该模式适合做一些类似播放音乐的操作。
	
	* START_REDELIVER_INTENT：若系统在onStartCommand()执行并返回后kill了service，那么service会被recreate并回调onStartCommand()并将最后一个Intent回传至该方法。任何 pending intents都会被轮流传递。该模式适合做一些类似下载文件的操作。
	
## 启动Service	

* startService()方法启动一个Service
	
	* 一旦启动，Service将一直运行在后台，即便启动Service的组件已被destroy。通常，一个被start的Service会在后台执行单独的操作，也并不给启动它的组件返回结果。

* bindService()方法绑定一个Service
	
	* 通过绑定方式启动的Service是一个client-server结构，该Service可以与绑定它的组件进行交互。一个bind service仅在有组件与其绑定时才会运行，多个组件可与一个service绑定，service不再与任何组件绑定时，该service会被destroy。


* 若需要启动Service，见下面所示：

		Intent intent = new Intent(this, HelloService.class);
		startService(intent);

* startService(intent)方法将立即返回，并回调onStartCommand()，若该Service未处于运行状态，系统将首先回调onCreate()，接着再回调onStartCommand()。若您希望Service可以返回结果，那么需要通过调用getBroadcast 返回的PendingIntent启动Service（将PendingIntent包装为Intent），service可使用broadcast 传递结果。

	多个启动Service的请求可能导致onStartCommand()多次调用，但只需调用stopSelf() 、 stopService()这两个方法之一，就可停止该服务。

* 停止服务

	一个启动的Service必须管理自己的生命周期。系统不会主动stop或destroy一个运行的Service，除非系统内存紧张，否则，执行完onStartCommand()方法后，Service依然运行。停止Service必须手动调用stopSelf()（在Service中）或调用stopService()（在启动组件中）。

	一旦调用了上述两种方法之一，系统会尽快destroy该Service.

	若系统正在处理多个调用onStartCommand()请求，那么在启动一个请求时，您不应当在此时停止该Service。为了避免这个问题，您可以调用stopSelf(int)方法，以确保请求停止的Service时最新的启动请求。这就是说，当调用stopSelf(int)方法时，传入的ID代表启动请求（该ID会传递至onStartCommand()），该ID与请求停止的ID一致。则如果在调用stopSelf(int)之前，Service收到一个新的Start请求，ID将无法匹配，Service并不会停止。

	为了节省内存和电量，当Service完成其工作后将其stop很有必要。如有必要，可以在其他组件中调用stopService()方法，即便Service处于绑定状态，只要它回调过onStartCommand()，也应当主动停止该Service。

* 创建绑定Service

	* 通过其他组件调用bindService()方法可以绑定一个Service以保持长连接，这时一般不允许其他组件调用startService()启动Service。

	* 当其他组件需要与Service交互或者需要跨进程通信时，可以创建一个bind Service。

	为创建一个bind Service，必须重写onBind()回调，该方法返回一个IBinder接口。该接口时组件与Service通信的桥梁。组件调用bindService()与Service绑定，该组件可获取IBinder接口，一旦获取该接口，就可以调用Service中的方法。一旦没有组件与Service绑定，系统将destroy它，您不必手动停止它。
	
	为创建一个bound Service，必须定义一个接口 ，该接口指定组件与Service如何通信。定义的接口在组件与Service之间，且必须实现IBinder接口。这正是onBind()的返回值。一旦组件接收了IBinder，组件与Service便可以开始通信。
	
	多个组件可同时与Service绑定，当组件与Service交互结束后，可调用unbindService()方法解绑。bound Service比start Service要复杂，故我将在后续单独翻译。

* 向用户发送通知（Sending Notifications to the User）

	运行中的Service可以通过Toast Notifications 或 Status Bar Notifications 向用户发送通知。Toast是一个可以短时间弹出的提醒框。二Status Bar是顶部状态栏中出现的太有图标的信息，用户可以通过下拉状态栏获得具体信息并执行某些操作（如启动Activity）。
	
	通常，Status Bar用于通知某些操作已经完成，如下载文件完成。当用户下拉状态栏后，点击该通知，可获取详细内容，如查看该下载的文件。
	
	运行前台Service（Running a Service in the Foreground）
	
	前台Service用于动态通知消息，如天气预报。该Service不易被kill。前台Service必须提供status bar，只有前台Service被destroy后，status bar才能消失。
	
	举例来说，一个播放音乐的Service必须是前台Service，只有这样用户才能确知其运行状态。为前台Service提供的status bar可以显示当前音乐的播放状态，并可以启动播放音乐的Activity。
	
	调用startForeground()可以启动前台Service。该方法接收两个参数，参数一是一个int型变量，用户指定该通知的唯一性标识，而参数而是一个Notification用于配置status bar，示例如下：

		Notification notification = new Notification(R.drawable.icon, getText(R.string.ticker_text),
		        System.currentTimeMillis());
		Intent notificationIntent = new Intent(this, ExampleActivity.class);
		PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, notificationIntent, 0);
		notification.setLatestEventInfo(this, getText(R.string.notification_title),
		        getText(R.string.notification_message), pendingIntent);
		startForeground(ONGOING_NOTIFICATION_ID, notification);

**注意：** 为startForeground()设置的ID必须是0。

调用stopForeground()来移除（remove）前台Service。该方法需传入一个boolean型变量，
表示是否也一并清除status bar上的notification（indicating whether to remove the status bar notification as well）。
该方法并不停止Service，如果停止正在前台运行的Service，那么notification 也一并被清除。

