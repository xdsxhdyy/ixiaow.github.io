---
layout: post
title:  "Android标题操作"
date:   2017-09-18
desc: "Android 标题的隐藏和ToolBar的使用"
keywords: "android,标题"
categories: [Android]
tags: [Android, toolbar, 标题]
---

随着Android 5.0之后，官方逐渐推荐使用Toolbar代替ActionBar

### 隐藏ActionBar
有两种方式可以隐藏ActionBar：

* java代码：
            
		//在activity中
		ActionBar actionBar = getSupportActionBar();
		if(actionBar != null){
		    actionBar.hide();
		}
* 设置主题：

		<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
		       <!-- Customize your theme here. -->
	        <!--<item name="colorPrimary">@color/colorPrimary</item>-->
	        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
	        <item name="colorAccent">@color/colorAccent</item>
	    </style>

### 将ActionBar替换成ToolBar

ToolBar 在Metrial设计中，官方建议替换成ActionBar
    
步骤 : 

* 将sytle中的Theme替换成NoActionBar
* 在布局文件中，添加toolbar控件
   
	   <android.support.v7.widget.Toolbar
	        android:id="@+id/toolbar"
	        android:layout_width="match_parent"
	        android:layout_height="?attr/actionBarSize"
	        android:background="?attr/colorPrimary"
	        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
	        app:popupTheme="@style/ThemeOverlay.AppCompat.Light">
	
	    </android.support.v7.widget.Toolbar>

* 新建菜单
    
	src --> res -->  menu--> main.xml
	    
* 在java代码中 引入menu

    
    * 方式一： 采用ActionBar的形式
    

        * 将ActionBar设置成ToolBar
        
			
				toolbar = (Toolbar)this.findViewById(R.id.toolbar);
		        setSupportActionBar(toolbar);
		        ActionBar actionBar = getSupportActionBar();
		        if (actionBar != null) {
		            actionBar.setDisplayHomeAsUpEnabled(true);
		            actionBar.setHomeAsUpIndicator(R.drawable.ic_menu);
		        }
		        
	    * 重写OnCreateOptionsMenu方法
		        
			    @Override
			    public boolean onCreateOptionsMenu(Menu menu) {
			        getMenuInflater().inflate(R.menu.main, menu);
			        return super.onCreateOptionsMenu(menu);
			    }

		* menu事件，重写onOptionsItemSelected方法，根据item.getId()判断
		        
			     @Override
				 public boolean onOptionsItemSelected(MenuItem item) {
			        return super.onOptionsItemSelected(item);
				 }
    * 方式二：利用Toolbar的api

		*  inflateMenu 加载menu   
				
				mToolbar.inflateMenu(menu_layout_id);
        
	    * menu事件 
			        
			    mToolbar.setOnMenuItemClickListener(menuItemClickListener);
   
<font color='red'>更多信息：</font> **https://xiaowujiang.cn/blog/**

自定义TitleView，请查看开源项目 Easy ：https://github.com/xiaowujiang/Easy

