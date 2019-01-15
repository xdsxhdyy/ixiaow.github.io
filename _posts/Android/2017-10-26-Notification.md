---
layout: post
title:  "Android Notification"
date:   2017-10-26
desc: "Android Notification"
keywords: "android, Notification"
categories: [Android]
tags: [Android, Notification]
---

### 普通通知

	public void showCustomNotification(View view){
        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
        mBuilder.setContentTitle("普通通知")
                .setContentText("普通通知的内容")
                .setTicker("普通通知来啦")
                .setWhen(System.currentTimeMillis())
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.push))
                .setDefaults(NotificationCompat.DEFAULT_ALL)
                .setAutoCancel(true)
                .setContentInfo("4")
                .setNumber(2);//当设置了ContentInfo后，number就会失效
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 1, new Intent(), PendingIntent.FLAG_UPDATE_CURRENT);
        mBuilder.setContentIntent(pendingIntent);

        ServiceUtil.getNotificationManager(this).notify(CUSTOM_NOTIFY, mBuilder.build());
    }

### BigTextStyle
    public void showBigTextStyle(View view) {
   	 	NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
    	mBuilder.setTicker("bigTextStyle 来啦")
            .setNumber(2)
            .setWhen(System.currentTimeMillis())
            .setAutoCancel(true)
            .setDefaults(NotificationCompat.DEFAULT_ALL)
            .setSmallIcon(R.mipmap.ic_launcher)
            .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.push));
    	android.support.v4.app.NotificationCompat.BigTextStyle bigTextStyle = new android.support.v4.app.NotificationCompat.BigTextStyle();
    	bigTextStyle.setBigContentTitle("bigContentTitle")
            .bigText("我是bigText, big text,我是bigText, big text,我是bigText, big text,我是bigText, big text,我是bigText, big text")
            .setSummaryText("我是summary text");
    	mBuilder.setStyle(bigTextStyle);
    	PendingIntent pendingIntent = PendingIntent.getActivity(this, 1, new Intent(), PendingIntent.FLAG_UPDATE_CURRENT);
    	mBuilder.setContentIntent(pendingIntent);
    		ServiceUtil.getNotificationManager(this).notify(BIGTEXT_STYLE, mBuilder.build());
	}
### InBoxStyle

	public void showInBoxStyle(View view) {
        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
        mBuilder.setTicker("inBoxStyle来啦")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.push))
                .setAutoCancel(true)
                .setContentInfo("你好")
                .setDefaults(NotificationCompat.DEFAULT_ALL);
        android.support.v4.app.NotificationCompat.InboxStyle inboxStyle =
                new android.support.v4.app.NotificationCompat.InboxStyle();
        inboxStyle.setBigContentTitle("inBoxStyle Title")
                .addLine("我是第一行，我是第一行，我是第一行，我是第一行，我是第一行，我是第一行，我是第一行，我是第一行")
                .addLine("我是第二行，第二行，第二行")
                .addLine("我是第三行")
                .setSummaryText("我是行末尾");
        mBuilder.setStyle(inboxStyle);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 1, new Intent(), PendingIntent.FLAG_UPDATE_CURRENT);
        mBuilder.setContentIntent(pendingIntent);
        ServiceUtil.getNotificationManager(this).notify(INBOXY_STYLE, mBuilder.build());
    }

### BigPictureStyle

	public void showBigPictureStyle(View view) {
        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
        mBuilder.setTicker("bigPictureStyle来啦")
                .setContentTitle("bigPictureStyle")
                .setContentText("bigPictureStyle Text")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.push))
                .setWhen(System.currentTimeMillis())
                .setContentInfo("这是一张图片")
                .setAutoCancel(true)
                .setDefaults(NotificationCompat.DEFAULT_ALL);
        android.support.v4.app.NotificationCompat.BigPictureStyle bigPictureStyle =
                new android.support.v4.app.NotificationCompat.BigPictureStyle();
        bigPictureStyle.setBigContentTitle("bigContentTitle")
                .bigPicture(BitmapFactory.decodeResource(getResources(), R.drawable.small))
                .setSummaryText("这是末尾....");
        mBuilder.setStyle(bigPictureStyle);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 1, new Intent(this, ImageActivity.class), PendingIntent.FLAG_UPDATE_CURRENT);
        mBuilder.setContentIntent(pendingIntent);
        ServiceUtil.getNotificationManager(this).notify(BIGPCITURE_STYLE, mBuilder.build());
    }

全屏

	  <style name="Theme.AppCompat.NoTitle.FullScreen" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="windowNoTitle">true</item>
        <item name="windowActionBar">false</item>
        <item name="android:windowFullscreen">true</item>
        <item name="android:windowContentOverlay">@null</item>
    </style>

### HangUp横幅通知

	public void showHangUp(View view){
        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
        mBuilder.setTicker("请在通知管理权限中开启权限")
                .setContentTitle("横幅通知")
                .setContentText("横幅通知内容...")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.drawable.push))
                .setWhen(System.currentTimeMillis())
                .setAutoCancel(false)
                .setDefaults(NotificationCompat.DEFAULT_ALL);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 1, new Intent(this, ImageActivity.class), PendingIntent.FLAG_UPDATE_CURRENT);
        mBuilder.setContentIntent(pendingIntent);
        //这句是关键
        mBuilder.setFullScreenIntent(pendingIntent, true);
        ServiceUtil.getNotificationManager(this).notify(HANG_UP, mBuilder.build());
    }

### 进度条对话框

	 private Notification getNotification(String msg, int progress, PendingIntent intent) {
        NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
        builder.setTicker("开始下载")
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.push))
                .setContentTitle(msg)
                .setAutoCancel(false);
        if (intent != null) {
            builder.setContentIntent(intent);
        }
        if (progress > 0) {
            builder.setContentText(progress + "%");
            builder.setProgress(100, progress, false);
        }
        return builder.build();
    }

### 自定义进度对话框

xml文件：

	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	android:layout_width="match_parent"
    	android:layout_height="wrap_content">

	    <ImageView
	        android:id="@+id/icon"
	        android:layout_width="50dip"
	        android:layout_height="50dip"
	        android:padding="5dip"
	        android:src="@drawable/push" />
	
	    <RelativeLayout
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        android:layout_toRightOf="@id/icon"
	        android:padding="5dip">
	
	        <TextView
	            android:id="@+id/title"
	            android:layout_width="match_parent"
	            android:layout_height="wrap_content"
	            android:text="我是title"
	            android:textSize="18sp" />
	
	        <ProgressBar
	            android:id="@+id/progress_bar"
	            style="?android:progressBarStyleHorizontal"
	            android:layout_width="match_parent"
	            android:layout_height="wrap_content"
	            android:layout_below="@id/title" />
	    </RelativeLayout>
	</RelativeLayout>

java：

	public void showCustomProgress(View view) {
        NotificationCompat.Builder builder = new NotificationCompat.Builder(this);
        RemoteViews remoteViews = new RemoteViews(getPackageName(), R.layout.custom_progress);
        remoteViews.setImageViewResource(R.id.image_view, R.drawable.push);
        remoteViews.setTextViewText(R.id.title, "正在下载...");
        remoteViews.setProgressBar(R.id.progress_bar, 100, 0, false);
        builder.setTicker("自定义progress来啦").setSmallIcon(R.mipmap.ic_launcher).setContent(remoteViews);
        ServiceUtil.getNotificationManager(this).notify(PROGRESS_BAR, builder.build());
    }
### 自定义View

xml:

	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	    android:layout_width="match_parent"
	    android:layout_height="wrap_content">
	
	    <ImageView
	        android:id="@+id/image_view"
	        android:layout_width="60dip"
	        android:layout_height="60dip"
	        android:layout_margin="5dip"
	        android:padding="5dip"
	        android:src="@drawable/push" />
	
	    <ImageButton
	        android:id="@+id/close"
	        style="@style/Widget.AppCompat.Button.Borderless"
	        android:layout_width="20dip"
	        android:layout_height="40dip"
	        android:layout_alignParentRight="true"
	        android:src="@drawable/ic_close_white" />
	
	    <TextView
	        android:id="@+id/song"
	        style="@style/TextAppearance.AppCompat.Notification.Title"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        android:layout_alignTop="@+id/image_view"
	        android:layout_toLeftOf="@+id/close"
	        android:layout_toRightOf="@id/image_view"
	        android:maxLines="1" />
	
	    <LinearLayout
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        android:layout_below="@id/close"
	        android:layout_toRightOf="@id/image_view"
	        android:gravity="center"
	        android:orientation="horizontal">
	
	        <ImageButton
	            android:id="@+id/pre"
	            style="@style/Widget.AppCompat.Button.Borderless"
	            android:layout_width="40dip"
	            android:layout_height="40dip"
	            android:src="@drawable/btn_prev" />
	
	        <ImageButton
	            android:id="@+id/pause"
	            style="@style/Widget.AppCompat.Button.Borderless"
	            android:layout_width="40dip"
	            android:layout_height="40dip"
	            android:src="@drawable/btn_pause" />
	
	        <ImageButton
	            style="@style/Widget.AppCompat.Button.Borderless"
	            android:layout_width="40dip"
	            android:layout_height="40dip"
	            android:src="@drawable/btn_next" />
	    </LinearLayout>
	
	</RelativeLayout>

java: 

	public void showCustomView(View view) {
        RemoteViews remoteViews = new RemoteViews(getPackageName(), R.layout.custom_view);
        remoteViews.setTextViewText(R.id.song, "我是歌曲名。我是歌曲名。我是歌曲名。我是歌曲名。我是歌曲名。。。。。。。。。。。");
        NotificationCompat.Builder mBuilder = new NotificationCompat.Builder(this);
        mBuilder.setSmallIcon(R.mipmap.ic_launcher).setTicker("开始播放")
                .setAutoCancel(false).setOngoing(true).setContent(remoteViews);
        ServiceUtil.getNotificationManager(this).notify(1, mBuilder.build());
    }
