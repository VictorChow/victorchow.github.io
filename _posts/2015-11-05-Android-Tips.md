---
layout: post
title: Android Tips
date: 2015-11-05
categories: code
tags: Android
---

> Android开发的备忘

1.有效的回车监听 editText.setEditorActionListener(), EditorInfo.IME_*，跟xml里imeOptions里一致 

2.自定义dialog去除自定义布局的上端多余白色背景：自定义style windowIsFloating = true，背景windowBackgroud设为透明; 

Activity中背景透明： 

android:theme="@android:style/Theme.Translucent" 

如果是acticity，继承theme.appcompat.dialog，两边加margin 

AppCompatActivity中背景透明： 

<style name="ActivityTransparent" parent="AppTheme"> 

​        <item name="android:windowNoTitle">true</item> 

​        <item name="android:windowIsTranslucent">true</item> 

​        <item name="android:windowBackground">@color/transparent</item> 

​    </style> 

3.addView时的LayoutParam需要新建，在addView的时候传进去，跟container的类型一致 

4.ViewPager不滑动的方法：onTouchEvent和onInterceptTouchEvent全return false 

5.AsyncTask的onPostExecute在UI线程执行，可以对控件进行操作 

6.TextView代码设置drawable方法，按原有的大小setCompoundDrawablesWithIntrinsicBounds，手动设置drawable.setBounds，setCompoundDrawables 

7.代码设置文字颜色selector，colorStateList=getResource().getColorStateList(R.color.…)，把selector文件从drawable文件夹移动到新建的color文件夹 

8.setDrawable()如果要设为null的话使用new ColorDrawable()，或者任意new Drawable() 

9.添加背景时不被拉伸方法：在drawable下新建bitmap，不平铺不拉抻 

<?xml version="1.0" encoding="utf-8"?> 

 <bitmap xmlns:android="http://schemas.android.com/apk/res/android" 

 android:gravity=“top" 

 android:src="@mipmap/icon1" 

 android:tileMode="disabled" /> 

10.textview 删除线 

textview.getPaint().setFlags(Paint.UNDERLINE_TEXT_FLAG); //下划线 

textview.getPaint().setFlags(Paint.STRIKE_THRU_TEXT_FLAG ); //中间横线 

textview.getPaint().setAntiAlias(true);// 抗锯齿 

11.fragment懒加载  重写setUserVisibleHint()方法 

12.设置所有activity跳转动画，application使用 

<style name="Anim_activity" parent="AppBaseTheme"> 

​    <item name="android:windowAnimationStyle">@style/activity_anim</item> 

</style> 

<style name="activity_anim" parent="@android:style/Animation.Activity"> 

​    <item name="android:activityOpenEnterAnimation">@anim/anim</item> 

​    <item name="android:activityOpenExitAnimation">@anim/anim</item> 

​    <item name="android:activityCloseEnterAnimation">@anim/anim</item> 

​    <item name="android:activityCloseExitAnimation">@anim/animt</item> 

</style> 

\13. 让radiogroup全部取消选中的黑科技：选中一个隐藏的。 

14.颜色渐变类ArgbEvaluator，调用evaluator(float, 0x,0x)方法, 配合ColorDrawable 

15.关于LayoutInflater类inflate(int resource, ViewGroup root, boolean attachToRoot)方法三个参数的含义：true：并且root存在，将xml挂载到root下，返回root；false：返回xml的根布局 

16.webView启用js：webview.getSettings().setJavaScriptEnabled(true); 

拦截url：webView.setWebViewClient(new WebViewClient() { 

public boolean shouldOverrideUrlLoading(WebView view, String url) { 

​        return true; 

​    } 

}); 

17.recycleView设置header，需要设置的spansize 

GridLayoutManager manager = new GridLayoutManager(mContext,2); 

manager.setSpanSizeLookup(new GridLayoutManager.SpanSizeLookup() { 

​    @Override 

​    public int getSpanSize(int position) { 

​        return 0; 

​    } 

}); 

18.Java进制换换 

（1）10转2、8、16 

Integer.toBinaryString() 

Integer.toOctalString() 

Integer.toHexString() 

（2）16、8、2转10 

Integer.parseInt(num, "x") 

19.关闭输入法 

if (getWindow().peekDecorView() != null) { 

​    InputMethodManager inputManger = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE); 

 inputManger.hideSoftInputFromWindow(getWindow().peekDecorView().getWindowToken(), 0); 

} 

打开输入法 

InputMethodManager inputManager =  (InputMethodManager) etSearch.getContext().getSystemService(Context.INPUT_METHOD_SERVICE); 

inputManager.showSoftInput(etSearch, 0); 

\20. 

ItemTouchHelper，RecyclerView移动和删除。 

\- 你想要控制其显示的方式，请通过布局管理器LayoutManager 

\- 你想要控制Item间的间隔（可绘制），请通过ItemDecoration 

\- 你想要控制Item增删的动画，请通过ItemAnimator 

21.触发点击声音,view.playSoundEffect(SoundEffectConstants.CLICK); 

22.java中的反射，用于访问类中的private变量或方法 

属性使用Filed类获取，方法使用Method类去调用。 

MyClass myClass = new MyClass();  //测试类 

Field field = Class.forName(MyClass.class.getName()).getDeclaredField("mName"); 

field.setAccessible(true); // 设置可获取 

Object name = field.get(myClass);  //取值 

field.set(myClass, "new name”);  //设置 

Method method = Class.forName(MyClass.class.getName()).getDeclaredMethod("print", new Class[]{int.class, String.class}); // 参数数组 

method.setAccessible(true); 

method.invoke(myClass, 100, "Victor”); //对应的参数 

23.activity全部统一动画，application设置theme 

<style name="Anim_activity" parent="AppTheme"> 

​    <item name="android:windowAnimationStyle">@style/activity_anim</item> 

</style> 

<style name="activity_anim" parent="@android:style/Animation.Activity"> 

​    <item name="android:activityOpenEnterAnimation">@anim/cu_push_right_in</item> 

​    <item name="android:activityCloseExitAnimation">@anim/cu_push_right_out</item> 

</style> 

24.Shader类可在canvas里实现渐变图形、混合渐变等 

25.recyclerView.computeVerticalScrollOffset() 计算纵向滑动距离 

26.FragmentManager 

fragment里用getChildFragmentManager() 

activity里用getSupportFragmentManager() 

27.Observable操作符大致分为以下几种： 

\- Creating Observables(Observable的创建操作符)，比如：Observable.create()、Observable.just()、Observable.from()等等； 

\- Transforming Observables(Observable的转换操作符)，比如：observable.map()、observable.flatMap()、observable.buffer()等等； 

\- Filtering Observables(Observable的过滤操作符)，比如：observable.filter()、observable.sample()、observable.take()等等； 

\- Combining Observables(Observable的组合操作符)，比如：observable.join()、observable.merge()、observable.combineLatest()等等； 

\- Error Handling Operators(Observable的错误处理操作符)，比如:observable.onErrorResumeNext()、observable.retry()等等； 

\- Observable Utility Operators(Observable的功能性操作符)，比如：observable.subscribeOn()、observable.observeOn()、observable.delay()等等； 

\- Conditional and Boolean Operators(Observable的条件操作符)，比如：observable.amb()、observable.contains()、observable.skipUntil()等等； 

\- Mathematical and Aggregate Operators(Observable数学运算及聚合操作符)，比如：observable.count()、observable.reduce()、observable.concat()等等； 

\- 其他如observable.toList()、observable.connect()、observable.publish()等等； 

28.canvas.drawArc 

​           SetStyle               UseCenter 

(1)           fill                            false 

(2)           fill                            true 

(3)        stroke                        false 

(4)        stroke                         true 

29.ViewGroup.setdescendantfocusability() 

\- FOCUS_BEFORE_DESCENDANTS ViewGroup本身先对焦点进行处理，如果没有处理则分发给child View进行处理 

\- FOCUS_AFTER_DESCENDANTS 先分发给Child View进行处理，如果所有的Child View都没有处理，则自己再处理 

\- FOCUS_BLOCK_DESCENDANTS ViewGroup本身进行处理，不管是否处理成功，都不会分发给Child View进行处理 

30.进入界面拦截EditText焦点，找父控件 

android:focusable="true"    android:focusableInTouchMode="true" 

\31. Resources类中的getIndentifier(name,defType,defPackage)方法，根据资源名次获取其ID； 

\32. View类的callOnClick(),performClick()和performLongClick()； 

33.TextView类中的append方法，追加文本； 

34.DecimalFormat类，用于字串格式化，包括指定位数，百分数和科学技术等 

35.getParent().requestDisallowInterceptTouchEvent(true);剥夺父view对touch事件的处理权 

36.  Palette，5.0加入的可以提取一个Bitmap中突出颜色的类，获取主题颜色。 

37.  FragmentManager.enableDebugLogging()，在需要观察 Fragment 状态的时候会有帮助。 

38.  Activity.recreate (),强制让 Activity 重建。 

39.  android:duplicateParentState(View)——此方法可以使得子 View 可以复制父 View 的状态。比如如果一个 ViewGroup 是可点击的，那么可以用这个方法在它被点击的时候让它的子 View 都改变状态。 

40.  android:enterFadeDuration/android:exitFadeDuration(Drawables)——此属性在 Drawable 具有多种状态的时候，可以定义它展示前的淡入淡出效果 

41.  SparseArray——Map的高效优化版本。姐妹类SparseBooleanArray、SparseIntArray和SparseLongArray。 

42.  ActivityManager.clearApplicationUserData()——一键清理你的app产生的用户数据，可能是做用户退出登录功能，有史以来最简单的方式了。 

43.  清除画布上的内容：canvas.drawColor(Color.TRANSPARENT, PorterDuff.Mode.CLEAR); 

44.  在自定义View的onDetachedFromWindow方法中清理与View相关的资源； 

\45.  选择使用ClipDrawable实现进度条功能； 

46.  自定义view中的getContext()，不需要专门创建一个mContext全局对象； 

47.两个activity共享元素，xml设置android:transitionName = “xxx", startActivity(intent, ActivityOptions.makeSceneTransitionAnimation(this, view,”xxx”).toBundle()); 如果有多个的话就用(this, Pair.create(view, “xxx1”),Pair.create(view, “xxx2”)).toBundle()); 

48.view圆形展开，用CircularReveal，用ViewAnimationUtils.createCircularReveal生成Animator然后设置相关属性 

49.跳转到另一个应用的activity： 

Intent i = new Intent(); 

i.setClassName(“com.centling.chuniang”(包名), “com.activity.MainActivity”(类名)); 

startActivity(i); 

用这个方法打开的activity和另一个应用里的这个activity是两个独立的 

50.在View中设置GestureDetector有两点需要注意： 

(1) View必须设置longClickable为true，否则手势识别无法正确工作，只会返回Down, Show, Long三种手势。 

(2) 必须在View的setOnTouchListener中调用手势识别，而不能重载onTouchEvent，否则手势识别无法正确工作。 

51.VelocityTracker使用时通过obtain获得实例，要在switch之前addMovement()，不然无效(不要在ACTION_DOWN里addMovement) 

52.解决滑动冲突，最好用第一种 

(1)外部拦截 重写viewGroup的intercept，其中down和up都返回false，move时自己需要此事件就拦截返回true 

(2)内部拦截 重写view的dispatch，使用parent.requestdisallow... 父控件需要就false,自己需要就true，return super.dispatch，另外重写父控件的intercept，down时返回false，其他true 

53. [Android中实现静态的默认安装和卸载应用](http://blog.csdn.net/jiangwei0910410003/article/details/36427963) <http://blog.csdn.net/jiangwei0910410003/article/details/36427963> 

54.注意setCustomAnimations()方法必须在add、remove、replace调用之前被设置，否则不起作用 

\55. Activity.moveTaskToBack(true) 模拟按home键返回桌面 

56.Android开发中一些被冷落但却很有用的类和方法 [http://luckyandyzhang.github.io/2016/02/04/Android开发中一些被冷落但却很有用的类和方法/](http://luckyandyzhang.github.io/2016/02/04/Android%E5%BC%80%E5%8F%91%E4%B8%AD%E4%B8%80%E4%BA%9B%E8%A2%AB%E5%86%B7%E8%90%BD%E4%BD%86%E5%8D%B4%E5%BE%88%E6%9C%89%E7%94%A8%E7%9A%84%E7%B1%BB%E5%92%8C%E6%96%B9%E6%B3%95/) 

57.36个Android开发常用代码片段 

<http://www.phpxs.com/code/1001775>

58.沉浸式状态栏 

(1) Activity里 

window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS); //透明状态栏 

window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION); //透明导航栏 

(2) xml里 父布局 里的最上面的布局添加 

android:clipToPadding=“true” (系统默认是true) 

android:fitsSystemWindows="true" 

59.反射里的tips： 

对已知的类用 类名.class.getXXX 

对未知的类用 Class.forName(类对象.getClass().getName()).getXXX，尤其是在源码里没有的类的对象使用时，效果显著 

60.wrap_content在可滑动的父控件里是UNSPECIFIED,在不可滑动的父控件里是AT_MOST 

61.播放系统声音 

val notification = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION) 

val r = RingtoneManager.getRingtone(applicationContext, notification) 

r.play() 

\62. 在项目里引用本地aar 

1) 添加本地仓库 

repositories { 

flatDir { dirs 'libs' } 

} 

2) compile(name: 'aar_name', ext: 'aar') 

\63. 判断是否有SD卡 Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED) 

\64. //在Activity应用<meta-data>元素。 ActivityInfo info = this.getPackageManager().getActivityInfo(getComponentName(),PackageManager.GET_META_DATA); info.metaData.getString("meta_name"); 

//在application应用<meta-data>元素。 ApplicationInfo appInfo = this.getPackageManager() .getApplicationInfo(getPackageName(),PackageManager.GET_META_DATA); appInfo.metaData.getString("meta_name"); 

//在service应用<meta-data>元素。 ComponentName cn = new ComponentName(this, MetaDataService.class); ServiceInfo info = this.getPackageManager().getServiceInfo(cn, PackageManager.GET_META_DATA); info.metaData.getString("meta_name"); 

//在receiver应用<meta-data>元素。 ComponentName cn = new ComponentName(context, MetaDataReceiver.class); ActivityInfo info = context.getPackageManager().getReceiverInfo(cn, PackageManager.GET_META_DATA); info.metaData.getString("meta_name"); 

\65. selector里的status_press必须在View已经设了点击事件时才有效 

\66. 查看keystore     keytool -list -v -keystore 

67.自己重写onMeasure时一定要先measureChildren 

\68. 禁止截屏 getWindow().addFlags(WindowManager.LayoutParams. FLAG_SECURE); 

\69. buildTypes { 

​        debug{ 

​            signingConfig signingConfigs.config 

​        } 

​        release { 

​            signingConfig signingConfigs.config 

​        } 

​    } 

70.给child用的attr, name后面加 _Layout 

71.自定义LayoutParams 要重写View的 generateDefaultLayoutParams 和两个generateLayoutParams 方法 

72.kotlin eap maven 

maven { url '<http://dl.bintray.com/kotlin/kotlin-eap>’ } 

\73. 全屏且有状态栏  

getWindow().addFlags(WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN); 

getWindow().addFlags(WindowManager.LayoutParams.FLAG_LAYOUT_NO_LIMITS); 

\74. app/build/intermediates/transforms/desugar/debug/folders 

75. pathMeasure.getPosTan(value * pathMeasure.getLength(), pos, tans) 

​      float degrees = (float) (Math.atan2(tans[1], tans[0]) * 180.0 / Math.PI) 

\76. poet生成通配符 

(1) java: Class<?>    kotlin: Class<*>     TypeVariableName.get(“*” / “?”) 

(2) java: Class<? extend A>    kotlin: Class<out A> 

ParameterizedTypeName.get() WildcardTypeName.subtypeOf() 

(3) java: Class<? super A>    kotlin: Class<in A> 

ParameterizedTypeName.get() WildcardTypeName.supertypeOf() 

77./Users/Victor/Projects/AndroidStudioProjects/sweeping_robot/app/build/generated/source/apt/emu/debug/com/centling/sweepingrobot/databinding 

78.AccessibilityService 

79.android:animateLayoutChanges add remove view时动画 可配合LayoutTransition自定义 

80.View生成Bitmap 

val screenshot = Bitmap.createBitmap(v.width, v.height), Bitmap.Config.ARGB_8888) 

val c = Canvas(screenshot) 

c.translate(-v.scrollX.toFloat(), -v.scrollY.toFloat()) 

v.draw(c) 

81.BitmapFactory.Options.inJustDecodeBounds 

如果inJustDecoedBounds设置为true的话，解码bitmap时可以只返回其高、宽和Mime类型，而不必为其申请内存，从而节省了内存空间 

82.TextView.setTransformationMethod 用来设置其中text的转换显示。ReplacementTransformationMethod可以自定义替换 例如把小写字母替换成大写字母 

83.Android自带的菜单PopupMenu ListPopupWindow