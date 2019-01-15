---
layout: post
title:  "Android FileProvider"
date:   2017-10-26
desc: "Android FileProvider"
keywords: "android, FileProvider"
categories: [Android]
tags: [Android, FileProvider]
---

## FileProvider

在android 7.0中，Android 框架执行的 StrictMode，API 禁止向您的应用外公开 file://URI。

在使用Uri.fromFile(file)，会让程序崩溃

处理方式：
	
java代码：

	if(Build.VERSION.SDK_INT > 24){//android 7.0
		imageUri = FileProvider.getUriForFile(MainActivity.this,
			"com.xiaowu.cameraalbumtest.fileprovider", outputImage);
	}else {
		imageUri = Uri.fromFile(outputImage);
	}

Android Manifest.xml

	<provider
		android:authorities="com.xiaowu.cameraalbumtest.fileprovider"
		android:name="android.support.v4.content.FileProvider"
		android:exported="false"
		android:grantUriPermissions="true">
		<meta-data
		android:name="android.support.FILE_PROVIDER_PATHS"
		android:resource="@xml/file_paths"/>
	
	</provider>

在res目录下新建xml目录，在xml目录下新建file_paths.xml
	
	<?xml version="1.0" encoding="utf-8"?>
	<paths>
		<external-path name="my_images" path=""/>
	</paths>

<files-path/>代表的根目录： 
	Context.getFilesDir()

<external-path/>代表的根目录: 
	Environment.getExternalStorageDirectory()

<cache-path/>代表的根目录: getCacheDir()