---
layout: post
title:  "Bitmap的加载和Cache"
date:   2017-10-26
desc: "Bitmap的加载和Cache"
keywords: "android, Bitmap"
categories: [Android]
tags: [Android, Bitmap]
---

## Bitmap高效加载 

Android应用程序都是有一定内存限制的，程序占用了过高的内存就容易出现OOM(OutOfMemory)异常。

* 查看每个应用程序的最最高可用内存：

		int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);  
		Log.d("TAG", "Max memory is " + maxMemory + "KB"); 
	 

因此在展示高分辨率图片的时候，最好先将图片进行压缩。压缩后的图片大小应该和用来展示它的控件大小相近。

### BitmapFactory

BitmapFactory类提供了四类方法： decodeFile、decodeResource、decodeStream和decodeByteArray,分别用于支持从文件系统、资源、输入流以及字节数组中加载Bitmap对象。

### BitmapFactory.Options参数

为了避免OOM异常，最好在解析每张图片的时候都先检查一下图片的大小，除非你非常信任图片的来源，保证这些图片都不会超出你程序的可用内存。

根据图片大小决定把整张图片加载到内存中还是加载一个压缩版的图片到内存中。以下几个因素是我们需要考虑的：

* 预估一下加载整张图片所需占用的内存。
* 为了加载这一张图片你所愿意提供多少内存。
* 用于展示这张图片的控件的实际大小。
* 当前设备的屏幕尺寸和分辨率。

比如，你的ImageView只有128\* 96像素的大小，只是为了显示一张缩略图，这时候把一张1024 \* 768像素的图片完全加载到内存中显然是不值得的。

##### 高效加载Bitmap的核心思想

* 采用BitmapFactory.Options来加载所需尺寸的图片。

##### 获取采样率的流程

通过采样率即可有效地加载图片

* 将BitmapFactory.Options的inJustDecodeBounds参数设置为true并加载图片
 
* 从BitmapFactory.Options中取出图片的原始宽高信息，它们对应于outWidth和outHeight参数

* 根据采样率的规则并结合目标View的所需大小计算出采样率inSampleSize

* 将BitmapFactory.Options的inJustDecodeBounds参数设置为false,然后重新加载图片。
当inJustDecodeBounds为true时BitmapFactory只会去解析图片的原始宽高信息，并不会真正地加载图片，故这个操作是轻量级的。并且BitmapFactory获取图片的宽高信息和图片的位置以及程序运行的设备有关。

##### 具体代码

* 获取图片原始大小

		BitmapFactory.Options options = new BitmapFactory.Options();  
		options.inJustDecodeBounds = true;  
		BitmapFactory.decodeResource(getResources(), R.id.myimage, options);  
		int imageHeight = options.outHeight;  
		int imageWidth = options.outWidth;  
		String imageType = options.outMimeType;

* 计算inSampleSize

	通过设置BitmapFactory.Options中inSampleSize的值就可以实现。比如我们有一张2048\*1536像素的图片，将inSampleSize的值设置为4，就可以把这张图片压缩成512\*384像素。原本加载这张图片需要占用13M的内存，压缩后就只需要占用0.75M了(假设图片是ARGB_8888类型，即每个像素点占用4个字节)。下面的方法可以根据传入的宽和高，计算出合适的inSampleSize值：

		public static int calculateInSampleSize(BitmapFactory.Options options,  
		        int reqWidth, int reqHeight) {  
		    // 源图片的高度和宽度  
		    final int height = options.outHeight;  
		    final int width = options.outWidth;  
		    int inSampleSize = 1;  
		    if (height > reqHeight || width > reqWidth) {  
		        // 计算出实际宽高和目标宽高的比率  
		        final int heightRatio = Math.round((float) height / (float) reqHeight);  
		        final int widthRatio = Math.round((float) width / (float) reqWidth);  
		        // 选择宽和高中最小的比率作为inSampleSize的值，这样可以保证最终图片的宽和高  
		        // 一定都会大于等于目标的宽和高。  
		        inSampleSize = heightRatio < widthRatio ? heightRatio : widthRatio;  
		    }  
		    return inSampleSize;  
		}  
* 根据inSampleSize压缩图片

		public static Bitmap decodeSampledBitmapFromResource(Resources res, int resId,int reqWidth, int reqHeight) {  
		    // 第一次解析将inJustDecodeBounds设置为true，来获取图片大小  
		    final BitmapFactory.Options options = new BitmapFactory.Options();  
		    options.inJustDecodeBounds = true;  
		    BitmapFactory.decodeResource(res, resId, options);  
		    // 调用上面定义的方法计算inSampleSize值  
		    options.inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight);  
		    // 使用获取到的inSampleSize值再次解析图片  
		    options.inJustDecodeBounds = false;  
		    return BitmapFactory.decodeResource(res, resId, options);  
		}  

* 下面的代码非常简单地将任意一张图片压缩成100*100的缩略图，并在ImageView上展示。

		mImageView.setImageBitmap(  
    	decodeSampledBitmapFromResource(getResources(), R.id.myimage, 100, 100));

## Android中的缓存策略

>目前比较常用的缓存策略是LruCache和DiskLruCache,其中LruCache常被用作内存缓存，DisKLruCache常被用作存储缓存。Lru是Least Recently Used的缩写，即最近最少使用算法，核心思想：当缓存快满时，会淘汰近期最少使用的缓存目标。

   
>LruCache是一个泛型类，它内部采用了一个LinkedHashMap以强引用的方式存储外界的缓存对象，其提供了get和put方法来提供完成缓存的获取和操作添加，当缓存满时，LruCache会移除较早使用的缓存对象，然后再添加新的缓存对象。

* 强引用：直接的对象引用
* 软引用： 当一个对象只有软引用存在时，系统内存不足时此对象会被GC回收

### LruCache 

* LruCache 是线程安全的

		public class LruCache<K,V>{
		    private final LinkedHashMap<K,V> map;
		}

* LruCache典型初始过程：

		int maxMemory = (int)(Runtime.getRuntime().maxMemory()/1024); //单位kb
		int cacheSize = maxMemory / 8;
		mMemoryCache = new LruCache<String, Bitmap>(cacheSize){
		    protected int sizeOf(String key, Bitmap bitmap){
		        return bitmap.gteRowBytes() * bitmap.getHeight() / 1024;
		    }
		}
在某些特殊情况下，还需要重写LruCache的entryRemoved方法，LruCache移除旧缓存时会调用，因此可以在entryRemoved中完成一些资源回收工作。

	private LruCache<String, Bitmap> mMemoryCache;  
		  
		@Override  
		protected void onCreate(Bundle savedInstanceState) {  
		    // 获取到可用内存的最大值，使用内存超出这个值会引起OutOfMemory异常。  
		    // LruCache通过构造函数传入缓存值，以KB为单位。  
		    int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);  
		    // 使用最大可用内存值的1/8作为缓存的大小。  
		    int cacheSize = maxMemory / 8;  
		    mMemoryCache = new LruCache<String, Bitmap>(cacheSize) {  
		        @Override  
		        protected int sizeOf(String key, Bitmap bitmap) {  
		            // 重写此方法来衡量每张图片的大小，默认返回图片数量。  
		            return bitmap.getByteCount() / 1024;  
		        }  
		    };  
		}  
		  
		public void addBitmapToMemoryCache(String key, Bitmap bitmap) {  
		    if (getBitmapFromMemCache(key) == null) {  
		        mMemoryCache.put(key, bitmap);  
		    }  
		}  
		  
		public Bitmap getBitmapFromMemCache(String key) {  
		    return mMemoryCache.get(key);  
		}  

### DiskLruCache

DiskLruCache用于实现存储设备缓存，即磁盘缓存，通过将缓存对象写入文件系统从而实现缓存的效果。

使用方式：

* DiskLruCache创建

	* DiskLruCache并不能通过构造方法来创建，它提供open方法创建自身
	   
			public static DiskLruCache open(File directory,int appVersion, int valueCount, long maxSize)
			
			//directory表示磁盘缓存在文件系统中的存储路径
			//这个路径可以选择SD卡上的缓存目录，具体指/sdcard/Android/data/package_name/cache 当应用被卸载后，此目录会一并删除
			//建议：如果应用卸载后就希望删除缓存文件，那么就选择卡上的缓存目录，如果希望保留缓存数据那就应该选择SD卡上的其他特定目录
			
			//appVersion 一般设为1即可，当版本号发生改变时DiskLruCache会清空之前所有的缓存文件，而这个特性在实际开发中作用并不大
			
			//valueCount表示节点对应的数据的个数，一般设为1即可。
			
			//maxSize 表示缓存的总大小，比如50MB,当缓存大小超出这个设定值后，DiskLruCache会清除一些缓存从而保证总大小不大于这个设定值

	* DiskLruCache的创建过程：

		    private static final long DISK_CACHE_SIZE = 1024* 1024*50; //50MB
		    File diskCacheDir = getDiskCacheDir(mContext, "bitmap");
		    if(!diskCacheDir.exists()){
		        diskCacheDir.mkdirs();
		    }
		    mDiskLruCache = DiskLruCache.open(diskCacheDir, 1, 1, DISK_CACHE_SIZE);

* DiskLruCache的缓存添加

    DiskLruCache的缓存添加的操作是通过Editor完成的，Editor表示一个缓存对象的编辑对象。这里仍然以图片缓存举例，首先需要获取图片url所对应的key，然后根据key就可以通过edit()来获取Editor对象，如果这个缓存正在被编辑，那么返回edit()会返回null,即DiskLruCache不允许同时编辑一个缓存对象。之所以要把url转换成key,是因为图片的url中很可能有特殊字符，这将影响url在android中直接使用，一般采用url的md5作为key。
    
    将图片的url转成key以后，就可以获取Editor对象。对于这个key来说，如果当前不存在其他Editor对象，那么edit()就会返回一个新的Editor对象，通过它就可以得到文件输出流。需要注意的是，由于前面在DiskLruCache的open方法中设置了一个节点只能有一个数据，因此下面的DISK\_CACHE\_INDEX常量直接设置为0即可。
    

	    String key = hashKeyFromUrl(url);
	    DiskLruCache.Editor editor = mDiskLruCache.edit(key);
	    if(editor != null){
	        OutputStream out = editor.newOutputStream(DISK_CHANE_INDEX);
	    }
	
	 	进行写入操作后，还必须通过commit()来提交写入操作。如果图片下载过程发生了异常，可以通过Editor的abort()来退回整个操作。

* DiskLruCache的缓存查找

	缓存查找过程也需要将url转换为key,通过DiskLruCache的get方法得到一个Snapshot对象，接着再通过Snapshot对象即可得到缓存的文件输入流，有了文件输出流，就可以得到Bitmap对象。为了避免加载图片过程中导致的OOM问题，一般不建议直接加载原始图片。通过BitmapFactory.Options对象来加载一张缩放后的图片，但那种方法对FileInputStream的缩放存在问题，原因是FileInputStream是一种有序的文件流，而两次decodeStream 调用影响了文件流的位置属性，导致了第二次decodeStream时的得到的是null。为了解决这个问题，可以通过文件流来得到它所对应的文件描述符，然后再通过BitmapFactory.decodeFileDescriptor方法来加载一张缩放后的图片

## 使用图片缓存技术

  
在很多情况下，（比如使用ListView, GridView 或者 ViewPager 这样的组件），屏幕上显示的图片可以通过滑动屏幕等事件不断地增加，最终导致OOM。

为了保证内存的使用始终维持在一个合理的范围，通常会把被移除屏幕的图片进行回收处理。此时垃圾回收器也会认为你不再持有这些图片的引用，从而对这些图片进行GC操作。用这种思路来解决问题是非常好的，可是为了能让程序快速运行，在界面上迅速地加载图片，你又必须要考虑到某些图片被回收之后，用户又将它重新滑入屏幕这种情况。这时重新去加载一遍刚刚加载过的图片无疑是性能的瓶颈，你需要想办法去避免这个情况的发生。

内存缓存技术对那些大量占用应用程序宝贵内存的图片提供了快速访问的方法。其中最核心的类是LruCache (此类在android-support-v4的包中提供) 。这个类非常适合用来缓存图片，它的主要算法原理是把最近使用的对象用强引用存储在 LinkedHashMap 中，并且把最近最少使用的对象在缓存值达到预设定值之前从内存中移除。

在过去，我们经常会使用一种非常流行的内存缓存技术的实现，即软引用或弱引用 (SoftReference or WeakReference)。但是现在已经不再推荐使用这种方式了，因为从 Android 2.3 (API Level 9)开始，垃圾回收器会更倾向于回收持有软引用或弱引用的对象，这让软引用和弱引用变得不再可靠。另外，Android 3.0 (API Level 11)中，图片的数据会存储在本地的内存当中，因而无法用一种可预见的方式将其释放，这就有潜在的风险造成应用程序的内存溢出并崩溃。

为了能够选择一个合适的缓存大小给LruCache, 有以下多个因素应该放入考虑范围内，例如：

* 你的设备可以为每个应用程序分配多大的内存？
设备屏幕上一次最多能显示多少张图片？有多少图片需要进行预加载，因为有可能很快也会显示在屏幕上？

* 你的设备的屏幕大小和分辨率分别是多少？一个超高分辨率的设备（例如 Galaxy Nexus) 比起一个较低分辨率的设备（例如 Nexus S），在持有相同数量图片的时候，需要更大的缓存空间。
图片的尺寸和大小，还有每张图片会占据多少内存空间。
图片被访问的频率有多高？会不会有一些图片的访问频率比其它图片要高？如果有的话，你也许应该让一些图片常驻在内存当中，或者使用多个LruCache 对象来区分不同组的图片。
你能维持好数量和质量之间的平衡吗？有些时候，存储多个低像素的图片，而在后台去开线程加载高像素的图片会更加的有效。

并没有一个指定的缓存大小可以满足所有的应用程序，这是由你决定的。你应该去分析程序内存的使用情况，然后制定出一个合适的解决方案。一个太小的缓存空间，有可能造成图片频繁地被释放和重新加载，这并没有好处。而一个太大的缓存空间，则有可能还是会引起 Java.lang.OutOfMemory 的异常。

下面是一个使用 LruCache 来缓存图片的例子：

	private LruCache<String, Bitmap> mMemoryCache;  
	  
	@Override  
	protected void onCreate(Bundle savedInstanceState) {  
	    // 获取到可用内存的最大值，使用内存超出这个值会引起OutOfMemory异常。  
	    // LruCache通过构造函数传入缓存值，以KB为单位。  
	    int maxMemory = (int) (Runtime.getRuntime().maxMemory() / 1024);  
	    // 使用最大可用内存值的1/8作为缓存的大小。  
	    int cacheSize = maxMemory / 8;  
	    mMemoryCache = new LruCache<String, Bitmap>(cacheSize) {  
	        @Override  
	        protected int sizeOf(String key, Bitmap bitmap) {  
	            // 重写此方法来衡量每张图片的大小，默认返回图片数量。  
	            return bitmap.getByteCount() / 1024;  
	        }  
	    };  
	}  
	public void addBitmapToMemoryCache(String key, Bitmap bitmap) {  
	    if (getBitmapFromMemCache(key) == null) {  
	        mMemoryCache.put(key, bitmap);  
	    }  
	}  
	public Bitmap getBitmapFromMemCache(String key) {  
	    return mMemoryCache.get(key);  
	}  

在这个例子当中，使用了系统分配给应用程序的八分之一内存来作为缓存大小。在中高配置的手机当中，这大概会有4兆(32/8)的缓存空间。一个全屏幕的 GridView 使用4张 800x480分辨率的图片来填充，则大概会占用1.5兆的空间(800*480*4)。因此，这个缓存大小可以存储2.5页的图片。
当向 ImageView 中加载一张图片时,首先会在 LruCache 的缓存中进行检查。如果找到了相应的键值，则会立刻更新ImageView ，否则开启一个后台线程来加载这张图片。

	public void loadBitmap(int resId, ImageView imageView) {  
	    final String imageKey = String.valueOf(resId);  
	    final Bitmap bitmap = getBitmapFromMemCache(imageKey);  
	    if (bitmap != null) {  
	        imageView.setImageBitmap(bitmap);  
	    } else {  
	        imageView.setImageResource(R.drawable.image_placeholder);  
	        BitmapWorkerTask task = new BitmapWorkerTask(imageView);  
	        task.execute(resId);  
	    }  
	}  
BitmapWorkerTask 还要把新加载的图片的键值对放到缓存中。

	class BitmapWorkerTask extends AsyncTask<Integer, Void, Bitmap> {  
	    // 在后台加载图片。  
	    @Override  
	    protected Bitmap doInBackground(Integer... params) {  
	        final Bitmap bitmap = decodeSampledBitmapFromResource(  
	                getResources(), params[0], 100, 100);  
	        addBitmapToMemoryCache(String.valueOf(params[0]), bitmap);  
	        return bitmap;  
	    }  
	}  

 