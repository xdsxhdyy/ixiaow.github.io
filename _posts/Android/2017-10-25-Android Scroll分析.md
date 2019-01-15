---
layout: post
title:  "Android Scroll分析"
date:   2017-10-25
desc: "Android Scroll分析"
keywords: "android,Android scroll"
categories: [Android]
tags: [Android, Scroll]
---

## android 滑动效果

#### android坐标系

   android系统提供了getLocationOnScreen(int[] location) 这样的方法来获取Android系统中点的位置
 
#### 视图坐标系

android中还有一种视图坐标系，它是用来表示子视图在父视图中的位置关系， 通过getX(),getY()来获取视图坐标系中的坐标

#### 触控事件---MotionEvent

MotionEvent封装了很多事件常量

* 获取坐标点，相对距离的方法：

    * View 提供的获取坐标的方法：
    
        * getTop(): 获取到的是View自身的顶边到其父布局顶边的距离
        * getLeft(): 获取到的是View自身的左边到其父布局左边的距离
        * getRight(): 获取到的是View自身的右边到器父布局右边的距离
        * getBottom(): 获取到的是View自身的下边到其父布局下边的距离
        
    * MotionEvent提供的方法：
    
        * getX(): 获取点击事件距离控件左边的距离，即视图坐标
        * getY(): 获取点击事件距离控件顶边的距离，即视图坐标
        * getRawX():获取点击事件距离整个屏幕左边的距离，即绝对坐标
        * getRawY(): 获取点击事件距离整个屏幕顶边的距离，即绝对坐标

## TouchSlop

TouchSlop是系统所识别出的被认为是滑动的最小距离。这是一个常量，和设备有关，

    ViewConfiguration.get(getContext()).getScaledTouchSlop();


## 实现滑动效果的七种方式

#### layout()方法

* 滑动时，通过每次回调onTouchEvent()来获取移动的坐标

    
        int x = (int)event.getX();
        int y = (int)event.getY();
    
    
    然后在ACTION_DOWN事件中记录触摸点的坐标
    
        //记录触摸点的坐标
        case MotionEvent.ACTION_DOWN:
        
            lastX = x;
            lastY = y;
        
        break;
    
    然后在ACTION_MOVE事件中计算偏移量
    
        case MotionEvent.ACTION_MOVE:
            //计算偏移量
            int offsetX = x- lastX;
            int offsetY = y - lastY;
            
            //在top,left,bottom, right中加上偏移量
            layout(getLeft() + offsetX, getTop() + offsetY, getRight()+ offsetX,     getBottom() + offsetY);
        
        break;

    或者使用绝对坐标系，getRawX(),getRawY(),==但是使用绝对坐标系的时候一定要注意在ACTION_MOVE的逻辑后要重新设置初始坐标，才能准确的获取到偏移量==
    
    
#### offsetLeftAndRight()与offsetTopAndBottom()

这个方法相当于系统提供的一个对左右、上下移动的api封装，当计算出偏移量后执行


    //同时对left和right进行偏移
    offsetLeftAndRight(offsetX);
    //同时对top和bottom进行偏移
    offsetTopAndBottom(offsetY);

后就可以重新进行View布局，效果与layout一样


#### LayoutParams

LayoutParams保存了一个View的布局参数。因此可以在程序中，通过改变LayoutParams来动态修改布局的位置参数，从而达到改变View位置的效果，计算偏移量的方法与上述方法一致，获取到偏移量之后，

    LinearLayout.LayoutParams layoutParams = (LinearLayout.LayoutParams)getLayoutParams();
    layoutParams.leftMargin = getLeft()+offsetX;
    layoutParams.topMargin = getTop()+offsetY;
    setLayoutParams(layoutParams);

需要注意的是，通过getLayoutParams()获取LayoutParams时，要根据父布局的类型来设置不同的类型

还可以使用ViewGroup.MarginLayoutParams来实现这样一个功能

    ViewGroup.MarginLayoutParams layoutParams = (ViewGroup.MarginLayoutParams)getLayoutParams();
    layoutParams.leftMargin = getLeft()+offsetX;
    layoutParams.topMargin = getTop()+offsetY;
    setLayoutParams(layoutParams);

#### scrollBy与scrollTo

scrollTo(x,y)表示移动到一个具体的点，scrollBy(dx,dy)表示移动的增量为dx,dy

与前几种方式相同，在获取偏移量后使用scrollBy来移动view


    int offsetX = x - lastX;
    int offsetY = y - lastY;
    scrollBy(offsetX, offsetY)


注意，scrollTo 和 scrollBy 并不是我们想要移动的东西，他们移动的都是view的content，即让view的内容移动，如果想要View移动，则需要找到他的父View


    ((View)getParent()).scrollBy(offsetX,offsetY);

如果scrollBy()中的参数dx和dy设置为正数，那么content将向坐标轴负方向移动；如果设置为负数，那么将向坐标轴正方向移动


#### Scroller

通过使用Scroller类，可以实现平滑移动的效果，而再不是瞬间的完成

* 初始化 Scroller类

        mScroller = new Scroller(context);

* 重写computeScroll()，实现模拟滑动

        public void computeScroll(){
            super.computeScroll();
            //判断Scroll是否执行完毕
            if(mScroller.computeScrollOffset()){
                ((View)getParent).scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
                //通过重绘来不断调用computeScroll
                invalidate();
            }
        }
* startScroll开启模拟过程

    * startScroll有两个重载方法
    
    
            public void startScroll(int startX, int startY,int dx,int dy, int duration)
            
            public void startScroll(int startX, int startY, int dx, int dy)
        
    
    可以通过getScrollX()和getScrollY() 来获取父视图中content所滑动到的点的坐标，不过要注意这个值的正负，它与scrollBy和scrollTo是一样的

#### 属性动画

#### ViewDragHelper

 support库中为我们提供了DrawerLayout和SlidingPaneLayout来帮助开发者实现侧边栏滑动的效果，这两个强大的布局背后有一个功能强大的类---ViewDragHelper,基本可以实现各种不同的滑动、拖放需求，因此这个方法也是各种滑动解决的终极方案。
 
* 初始化ViewDragHelper

    
        //第一个参数指要监听的view,通常是一个ViewGroup,即parentView
        //第二个参数是一个CallBack回调，他是整个ViewDragHelper的逻辑核心
        mViewDragHelper = ViewDragHelper.create(this, callback);
        
    
* 拦截事件

        public boolean onInterceptTouchEvent(MotionEvent ev){
            return mViewDragHelper.shouldInterceptTouchEvent(ev);
        }
        
        public boolean onTouchEvent(MotionEvent ev){
            mViewDragHelper.processTouchEvent(event);
            return true;
        }
    
* 处理computeScroll()

    ViewDragHelper内部是通过Scroller来实现平滑移动的，模版代码：
   
        public void computeScroll(){
            if(mViewDragHelper.continueSettling(true)){
                ViewCompat.postInvalidateOnAnimation(this);
            }
        }
    
* 处理回调Callback

        private ViewDragHelper.Callback callback = new ViewDragHelper.Callback(){
        
            //何时开始监测触摸事件
            public boolean tryCaptureView(View child, int pointerId){
                //return child == mMainView开始时监测
                return false;
            }
        }

* 滑动方法


        public int clampViewPositionVertical(View child, int top, int dy){
            return top;
        }
        
        public int clampViewPositionHorizontal(View child, int left, int dx){
            
        }

## GestureDetector

手势检测，用于辅助检测用户的单击，滑动，长按，双击等。

* 创建一个GestureDetector对象并实现OnGestureListener接口，根据需要还可以实现OnDubleTapListener从而监听双击事件

        GestureDetector mGestureDetector = new GestureDetector(this);
        //解决长按后无法拖动的现象
        mGestureDetector.setIsLongpressEnable(false);


* 接管View的OnTouchEvent()

        boolean consume = mGestureDetector.onTouchEvent(envent);
        return consume;

参考建议：如果只是监听滑动相关的，建议在自己OnTouchEvent()实现，如果是监听双击这种行为的话，那么就使用GestureDetector