---
layout: post
title: Android Tips
date: 2017-10-16
categories: code
tags: Android
---

> 从实习到现在Android开发的备忘

---

**有效的回车监听**

 `editText.setEditorActionListener()`, `EditorInfo.IME_XXX`，跟xml里`imeOptions`里一致 

---

**自定义dialog去除自定义布局的上端多余白色背景**

style里 `windowIsFloating = true`

---

**AppCompatActivity中背景透明** 

```xml
<style name="ActivityTransparent" parent="AppTheme"> 
        <item name="android:windowNoTitle">true</item> 
        <item name="android:windowIsTranslucent">true</item> 
        <item name="android:windowBackground">@color/transparent</item> 
</style> 
```

---

`addView`时的LayoutParam需要新建，在`addView`的时候传进去，跟container的类型一致 

---

**ViewPager不滑动的方法**

`onTouchEvent`和`onInterceptTouchEvent`全`return false`

---

AsyncTask的`onPostExecute`在UI线程执行，可以对控件进行操作 

---

**TextView代码设置drawable方法**

按原有的大小`setCompoundDrawablesWithIntrinsicBounds`

手动设置`drawable.setBounds`  `setCompoundDrawables`

---

**代码设置文字颜色selector**

把selector文件移动到color文件夹

`ColorStateList colorStateList = getResource().getColorStateList(R.color.XXX)`

---

`setDrawable()`如果要设为null的话使用`new ColorDrawable()`，或者任意`new Drawable()`

---

添加背景时不被拉伸方法：在drawable下新建bitmap，不平铺不拉抻 

```xml
<?xml version="1.0" encoding="utf-8"?> 
<bitmap xmlns:android="http://schemas.android.com/apk/res/android" 
 android:gravity=“top" 
 android:src="@mipmap/icon1" 
 android:tileMode="disabled" /> 
```

---

**Textview 删除线** 

```java
//下划线 
textview.getPaint().setFlags(Paint.UNDERLINE_TEXT_FLAG); 
//中间横线 
textview.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG); 
// 抗锯齿 
textview.getPaint().setAntiAlias(true);
```

---

**Fragment懒加载** 

重写`setUserVisibleHint()`方法 

---

**设置所有activity跳转动画，application使用** 

```xml
<style name="Anim_activity" parent="AppBaseTheme"> 
    <item name="android:windowAnimationStyle">@style/activity_anim</item> 
</style> 

<style name="activity_anim" parent="@android:style/Animation.Activity"> 
    <item name="android:activityOpenEnterAnimation">@anim/anim</item> 
    <item name="android:activityOpenExitAnimation">@anim/anim</item> 
    <item name="android:activityCloseEnterAnimation">@anim/anim</item> 
    <item name="android:activityCloseExitAnimation">@anim/animt</item> 
</style> 
```

---

**让RadioGroup全部取消选中的小黑科技**

选中一个隐藏的

---

**颜色渐变类ArgbEvaluator**

调用`evaluator(float, 0x,0x)`方法, 配合`ColorDrawable`

---

**关于LayoutInflater的inflate**

`inflate(int resource, ViewGroup root, boolean attachToRoot)`方法三个参数的含义：true：并且root存在，将xml挂载到root下，返回root；false：返回xml的根布局 

---

**WebView**初级使用

```java
//启用js
webview.getSettings().setJavaScriptEnabled(true); 
//拦截url
webView.setWebViewClient(new WebViewClient() { 
public boolean shouldOverrideUrlLoading(WebView view, String url) { 
        return true; 
    } 
}); 
```

---

**RecycleView设置header**

需要设置的SpanSize 

```java
GridLayoutManager manager = new GridLayoutManager(mContext,2); 
manager.setSpanSizeLookup(new GridLayoutManager.SpanSizeLookup() { 
    @Override 
    public int getSpanSize(int position) { 
        return 2; 
    } 
}); 

```

---

**Java进制转换**

10转2、8、16 

```java
Integer.toBinaryString() 
Integer.toOctalString() 
Integer.toHexString() 
```

16、8、2转10 

```java
Integer.parseInt(num, "x") 
```

---

**关闭输入法** 

```java
if (getWindow().peekDecorView() != null) { 
    InputMethodManager inputManger = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE); 
    inputManger.hideSoftInputFromWindow(getWindow().peekDecorView().getWindowToken(), 0); 
} 
```

**打开输入法** 

```java
InputMethodManager inputManager =  (InputMethodManager) etSearch.getContext().getSystemService(Context.INPUT_METHOD_SERVICE); 
inputManager.showSoftInput(etSearch, 0);
```

---

RecyclerView移动和删除：`ItemTouchHelper`

控制其显示的方式：`LayoutManager` 

控制Item间的间隔（可绘制）：`ItemDecoration`

控制Item增删的动画：`ItemAnimator` 

---

**触发点击声音**

```java
view.playSoundEffect(SoundEffectConstants.CLICK);
```

---

**Java中的反射**

用于获取、设置类中的private变量或方法 

属性使用Filed类获取，方法使用Method类去调用。 

```java
MyClass myClass = new MyClass();  //测试类 
Field field = Class.forName(MyClass.class.getName()).getDeclaredField("mName"); 
field.setAccessible(true); // 设置可获取 
Object name = field.get(myClass);  //取值 
field.set(myClass, "new name”);  //设置 
Method method = Class.forName(MyClass.class.getName()).getDeclaredMethod("print", new Class[]{int.class, String.class}); // 参数数组 
method.setAccessible(true); 
method.invoke(myClass, 100, "Victor”); //对应的参数 
```

---

`Shader`类可在canvas里实现渐变图形、混合渐变等 

---

RecyclerView.computeVerticalScrollOffset() 计算纵向滑动距离 

---

**FragmentManager** 

Fragment里用`getChildFragmentManager()`

Activity里用`getSupportFragmentManager()`

---

**Observable操作符大致分为以下几种**

创建：`Observable.create()` `Observable.just()` `Observable.from()`等 

转换：`observable.map()` `observable.flatMap()` `observable.buffer()`等

过滤：`observable.filter()` `observable.sample()` `observable.take()`等

组合：`observable.join()` `observable.merge()` `observable.combineLatest()`等

错误处理： `observable.onErrorResumeNext()` `observable.retry()`等 

功能：`observable.subscribeOn()` `observable.observeOn()` `observable.delay()`等

条件：`observable.amb()` `observable.contains()` `observable.skipUntil()`等

运算、聚合如：`observable.count()` `observable.reduce()` `observable.concat()`等

其他：`observable.toList()` `observable.connect()` `observable.publish()`等

---

**ViewGroup.setdescendantfocusability()** 

`FOCUS_BEFORE_DESCENDANTS` ViewGroup本身先对焦点进行处理，如果没有处理则分发给child View进行处理 

`FOCUS_AFTER_DESCENDANTS` 先分发给Child View进行处理，如果所有的Child View都没有处理，则自己再处理 

`FOCUS_BLOCK_DESCENDANTS` ViewGroup本身进行处理，不管是否处理成功，都不会分发给Child View进行处理 

---

**进入界面拦截EditText焦点**

找父控件 

`android:focusable="true"`   

`android:focusableInTouchMode="true"`

---

Resources类中的`getIndentifier(name, defType, defPackage)`方法，根据资源名字获取其ID

---

View类的`callOnClick()` `performClick()`和`performLongClick()`

---

TextView类中的`append`方法，追加文本

---

**DecimalFormat**

用于字串格式化，包括指定位数，百分数和科学技术等 

---

**剥夺父view对touch事件的处理权** 

```java
getParent().requestDisallowInterceptTouchEvent(true);
```

---

**Palette**

Android 5.0加入的可以提取一个Bitmap中突出颜色的类，可用于获取主题颜色

---

`FragmentManager.enableDebugLogging()`，在需要观察 Fragment 状态的时候会有帮助

---

`Activity.recreate()`，强制让 Activity 重建

---

 **Drawable淡入淡出效果**

`android:enterFadeDuration` `android:exitFadeDuration`

此属性在 Drawable 具有多种状态的时候，可以定义它展示前的淡入淡出效果

---

**SparseArray**

Map的高效优化版本。

姐妹类`SparseBooleanArray`、`SparseIntArray`和`SparseLongArray`。 

---

**ActivityManager.clearApplicationUserData()**

一键清理你的app产生的用户数据，可能是做用户退出登录功能，有史以来最简单的方式了。

---

**清除画布上的内容**

```java
canvas.drawColor(Color.TRANSPARENT, PorterDuff.Mode.CLEAR); 
```

---

在自定义View的`onDetachedFromWindow`方法中清理与View相关的资源

---

选择使用ClipDrawable实现进度条功能

---

自定义view中有`getContext()`，不需要专门创建一个mContext全局对象

---

**Activity共享元素**

xml设置`android:transitionName = “xxx"`

```java
startActivity(intent, ActivityOptions.makeSceneTransitionAnimation(this, view,”xxx”).toBundle())
```

 如果有多个的话就用

```java
startActivity(intent, ActivityOptions.makeSceneTransitionAnimation(this, Pair.create(view, “xxx1”),Pair.create(view, “xxx2”)).toBundle())
```

----

**View圆形展开用CircularReveal**

用`ViewAnimationUtils.createCircularReveal`生成`Animator`然后设置相关属性 

---

**跳转到另一个应用的Activity** 

```jave
//用这个方法打开的activity和另一个应用里的这个activity是两个独立的 
Intent i = new Intent(); 
i.setClassName("com.centling.chuniang", "com.activity.MainActivity"); 
startActivity(i); 
```

---

**在View中设置GestureDetector有两点需要注意**

View必须设置`longClickable`为`true`，否则手势识别无法正确工作，只会返回Down, Show, Long三种手势。 

必须在View的`setOnTouchListener`中调用手势识别，而不能重写`onTouchEvent`，否则手势识别无法正确工作。 

---

`VelocityTracker`使用时通过`obtain`获得实例，要在Switch Event之前`addMovement()`，不然无效，不要在`ACTION_DOWN`里`addMovement`。

---

**解决滑动冲突**

外部拦截：重写viewGroup的intercept，其中down和up都返回false，move时自己需要此事件就拦截返回true 

内部拦截：重写view的dispatch，使用parent.requestdisallow... 父控件需要就false,自己需要就true，return super.dispatch，另外重写父控件的intercept，down时返回false，其他true 

---

[Android中实现静态的默认安装和卸载应用](http://blog.csdn.net/jiangwei0910410003/article/details/36427963)

---

**Fragment动画**

`setCustomAnimations()`方法必须在`add`、`remove`、`replace`调用之前被设置，否则无效。

---

**模拟按home键返回桌面** 

`Activity.moveTaskToBack(true) `

---

[36个Android开发常用代码片段](<http://www.phpxs.com/code/1001775>)

---

**沉浸式状态栏** 

(1) Activity里 

`window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS); //透明状态栏 `

`window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION); //透明导航栏 `

(2) xml里 父布局 里的最上面的布局添加 

`android:clipToPadding=“true”` (系统默认是true) 

`android:fitsSystemWindows="true"` 

---

**反射的Tips：** 

对已知的类用 类名.class.getXXX 

对未知的类用 Class.forName(类对象.getClass().getName()).getXXX，尤其是在源码里没有的类的对象使用时，效果显著 

---

`wrap_content`在可滑动的父控件里是`UNSPECIFIED`，在不可滑动的父控件里是`AT_MOST`

---

**播放系统声音** 

```kotlin
val notification = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION) 
val r = RingtoneManager.getRingtone(applicationContext, notification) 
r.play() 
```

---

**在项目里引用本地aar** 

添加本地仓库 

```groovy
repositories { 
  flatDir { dirs 'libs' } 
} 
```

build.gradle

```groovy
compile(name: 'aar_name', ext: 'aar') 
```

---

**判断是否有SD卡** 

```java
Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)
```

---

在Activity应用`<meta-data>`元素

```java
ActivityInfo info = this.getPackageManager().getActivityInfo(getComponentName(), PackageManager.GET_META_DATA); 
info.metaData.getString("meta_name"); 
```

在application应用`<meta-data>`元素

```java
ApplicationInfo appInfo = this.getPackageManager() .getApplicationInfo(getPackageName(), PackageManager.GET_META_DATA); 
appInfo.metaData.getString("meta_name"); 
```

在service应用`<meta-data>`元素

```java
ComponentName cn = new ComponentName(this, MetaDataService.class);
ServiceInfo info = this.getPackageManager().getServiceInfo(cn, PackageManager.GET_META_DATA); info.metaData.getString("meta_name"); 
```

在receiver应用`<meta-data>`元素。

```java
ComponentName cn = new ComponentName(context, MetaDataReceiver.class); 
ReceiverInfo info = context.getPackageManager().getReceiverInfo(cn, PackageManager.GET_META_DATA); info.metaData.getString("meta_name");
```

---

selector里的status_press必须在View已经设了点击事件时才有效 

---

**查看keystore**

`keytool -list -v -keystore`

---

重写onMeasure时一定要先`measureChildren`

---

**禁止截屏**

```java
getWindow().addFlags(WindowManager.LayoutParams. FLAG_SECURE); 
```

---

给child用的attr, name后面加 _Layout 

---

**自定义LayoutParams**

要重写View的`generateDefaultLayoutParams`和两个`generateLayoutParams`方法 

---

**全屏且有状态栏**  

```java
getWindow().addFlags(WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN); 
getWindow().addFlags(WindowManager.LayoutParams.FLAG_LAYOUT_NO_LIMITS); 
```

---

**PathMeasure获取角度**

```java
pathMeasure.getPosTan(value * pathMeasure.getLength(), pos, tans) 
float degrees = (float) (Math.atan2(tans[1], tans[0]) * 180.0 / Math.PI) 
```

---

**poet生成通配符** 



(1) java: `Class<?>`    kotlin: `Class<*>`    

`TypeVariableName.get(“*” / “?”)` 

(2) java: `Class<? extend A>`    kotlin: `Class<out A>` 

`ParameterizedTypeName.get()`  `WildcardTypeName.subtypeOf()` 

(3) java: `Class<? super A>`    kotlin: `Class<in A>` 

`ParameterizedTypeName.get()`  `WildcardTypeName.supertypeOf()`

---

**AccessibilityService** 

---

`android:animateLayoutChanges` add remove view时动画 可配合LayoutTransition自定义 

---

**View生成Bitmap** 

```kotlin
val screenshot = Bitmap.createBitmap(v.width, v.height), Bitmap.Config.ARGB_8888) 
val c = Canvas(screenshot) 
c.translate(-v.scrollX.toFloat(), -v.scrollY.toFloat()) 
v.draw(c) 
```

---

`BitmapFactory.Options.inJustDecodeBounds`

如果inJustDecoedBounds设置为true的话，解码bitmap时可以只返回其高、宽和Mime类型，而不必为其申请内存，从而节省了内存空间 

---

`TextView.setTransformationMethod` 用来设置其中text的转换显示。

`ReplacementTransformationMethod`可以自定义替换 例如把小写字母替换成大写字母。

---

Android自带的菜单`PopupMenu` `ListPopupWindow`

---