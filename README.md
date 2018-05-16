# Android-
工作五年多了，一直没有仔细回顾过过去，正好现在将要离职时间比较空闲，整理一下android方面的知识点  
另外点名批评有道云，我整理笔记三次丢失大量笔记，总计浪费我26个小时的时间，其实后边还有不少，但是第三次丢失了，是在心累，不想写了  
**Android总结**

[1 Android基础知识](#jump1)|[2 异步消息处理机制](#jump2)|[3 View相关技术](#jump3)|[4 Android构建](#jump4)|[5 开源框架源码](#jump5)|[6 Android异常与性能优化](#jump6)|[7 热门前沿知识](#jump7)|[8 Java高级技术](#jump8)
---|---|---|---|---|---|---|---
[1.1 Frgment](#jump1.1)|[2.1 Handler](#jump2.1)|[3.1 View的渲染和绘制](#jump3.1)|[4.1 android编译打包](#jump4.1)|[5.1 Okhttp网络框架](#jump5.1)|[6.1 ANR](#jump6.1)|[7.1 MVC架构设计模式](#jump7.1)|[8.1 IO相关-Socket](#jump8.1)
[1.2 Activity](#jump1.2)|[2.2 IntentService](#jump2.2)|[3.2 事件分发](#jump3.2)|[4.2 Proguard混淆](#jump4.2)|[5.2 Retrofit网络框架](#jump5.2)|[6.2 OOM](#jump6.2)|[7.2 MVP架构设计模式](#jump7.2)|[8.2 IO相关-BIO／NIO](#jump8.2)
[1.3 Service](#jump1.3)|[2.3 AsyncTask](#jump2.3)|[3.3 ListView](#jump3.3)|[4.3 Git](#jump4.3)|[5.3 Volley网络框架](#jump5.3)|[6.3 Bitmap](#jump6.3)|[ 7.3 MVVM架构设计模式](#jump7.3)|[8.3 多线程相关](#jump8.3)
[1.4 BroadcastReceiver广播](#jump1.4)|[2.4 HandlerThread](#jump2.4)| |[4.4 Gradle语法](#jump4.4)|[5.4 butterknife注解框架](#jump5.4)|  [6.4 UI卡顿](#jump6.4)| [ 7.4 android插件化](#jump7.4)
[1.5 Binder跨进程通讯](#jump1.5)| | | |[5.5 Glide图片框架](#jump5.5)| [6.5 内存泄漏](#jump6.5) |[7.5 android热更新](#jump7.5)
[1.6 Android异常与性能优化](#jump1.6)| | | | | [6.6 内存管理](#jump6.6) |[7.6 进程保活](#jump7.6)
| | | | | |[6.7 冷启动优化](#jump6.7)| 
| | | | | |[6.8 其他优化](#jump6.8)| 


<span id = "jump1">

# 一 Android基础知识

</span>


<span id = "jump1.1">

## 1.1 Frgment

</span>


### 1.1.1 Fragment为什么被称为第五大组件

#### 1.1.1.1 Fragment为什么被称为第五大组件

实用频率不输与其他四大组件，
不像View没有生命周期，Android3.0为了大屏幕展现UI，灵活的加载到activity中，
相比activity更节省内存，UI切换效果更佳舒适。

#### 1.1.1.2 Fragment加载到Activity的两种方式

1 静态加载：添加Fragment到Activity的布局文件当中  

2 动态在activity中添加Fragment  
```
//第一步 添加一个FragmentTransaction的实例
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction transaction = fragmentManager.beginTransaction();

//第二步 用add()方法加上Fragment的对象rightFragment
RightFragment rightFragment = new RightFragment();
transaction.add(R.id.xxx,rightFragment,"rightFragment");
transaction.addToBackStack("rightFragment");

//第三步 调用commit()方法使FragmentTransaction实例的改变生效
transaction.commit();

```

#### 1.1.1.3 FragmentPagerAdapter与FragmentStatePagerAdapter区别

FragmentPagerAdapter适用于页面较少的情况，不回收内存   

FragmentStatePagerAdapter适用于页面较多的情况，会去释放Franment内存  

### 1.1.2 Fragment的生命周期

开始创建Fragment对象：(F:Fragment的生命周期,A:Activity的生命周期)  
   onAttach(F)Fragment和Activity关联之后的回调  
-> onCreate()初次创建，只是创建，此时activity还未创建  
-> onCreateView(F)首次绘制用户界面的回调  
-> onViewCreated(F)Fragment的UI已经绘制完成  
-> A-onCreate()  
-> onActivityCreated(F)activity渲染绘制成功之后  
-> A-onStart()activity可见了  
-> onStart(F)Fragment可见了  
-> A-onResume()activity可交互了  
-> onResume()Fragment完全初始化完毕，可交互了  
-> onPause(F)不能交互了  
-> A-onPause()  
-> onStop(F)  
-> A-onStop()  
-> onDestoryView(F)即将结束  
-> onDestory()  
-> onDetach(F)Fragment对象销毁  
-> A-onDetach()  

### 1.1.3 Fragment之间的通信

1 在Fragment中调用activity中的方法：只需要调用getActivity();  
2 在Activity中调用Fragment的方法：接口回调;  
3 在Fragment中调用Fragment的方法：getActivity().findFragmentByid();  

### 1.1.4 Fragment管理器:FragmentManager

replace:替换Fragment实例  
add:将Fragment实例添加到activity的最上层  
remove:将Fragment实例添加到activity的Fragment队列中删除  


<span id = "jump1.2">

## 1.2 Activity

</span>

### 1.2.1 activity生命周期

Android提供给用户操作的界面

#### 1.2.1.1 activity的4中状态

running：活动状态，栈顶  

paused：失去焦点时，操作无反应  

stopped：被覆盖不可见时  

killed：已经被回收  

#### 1.2.1.2 activity生命周期分析

Activity启动 ： onCreate(创建时) -> onStart(正在启动，已经处于可见，还不可操作) -> onResume(可见，可交互)  

点击Home键回到主界面(Activity不可见) ：onPause(处于停止状态，处于可见，还不可操作) -> onStop(已经停止被完全覆盖)  

再次回到Activity时：onRestart(将要重新启动，可见到不可见时) -> onStart(可见的) -> onResume(可交互)  

退出当前Activity时: onPause() -> onStop() -> onDestroy(正在被销毁，回收工作，资源释放)

#### 1.2.1.3 android进程优先级

前台：处于这个在与用户交互的activity，或在前台activity绑定的Service  

可见：处于可见，不可操作  

服务：Service   

后台：按Home键后的前台进程  

空：缓存  

### 1.2.2 android任务栈 Stack

不是唯一的  

### 1.2.3 activity启动模式

1.standsrd: 标准模式，每次启动activity重新创建加入栈中，不会复用生命周期完整，消耗资源较大   

2.singletop：栈顶复用模式，判断栈顶复用  

3.singletask：栈内复用模式，单例模式，判断栈内复用，并销毁当前activity之上的，并回调onNewIntent()  

4.singleinstance: 整个系统中有且只有一个实例，这个activity独享挣个任务栈  

### 1.2.4 scheme跳转协议

Android中scheme时一种页面内跳转协议，是一种非常好的实现机制  
通过定义自己的scheme协议，可以非常方便的跳转APP中各个页面（APP通过URL跳转另一个APP指定页面）  
通过scheme协议，服务器可以定制化告诉APP跳转哪个页面（服务端下发URL路径，客户端根据路径跳转activity）  
可以通过通知栏消息定制化跳转页面，可以通过H5页面跳转页面等（从浏览器中启动activity）。


<span id = "jump1.3">

## 1.3 Service

</span>


onCreate()首次创建服务时  
onBind()绑定服务时才会调用（bindService）   
onStartCommand()是startService()方法启动Service时都会被回调，一旦调用服务正式开启  
onDsetroy()服务销毁时的回调，注意资源回收  

### 1.3.1 Service的应用场景，以及和Thtrad区别

#### 1.3.1.1 Service是什么？

可以在后台执行长时间运行操作而没有用户界面的应用组件，可由其他组件启动，一旦启动长时运行，即使启动它的组件已经被销毁  
可以绑定到activity进行数据交互，或通过进程间通讯  
运行在主线程中不能做长时间的耗时操作  
在后台处理一些长时逻辑，执行长时间运行的任务  
程序退出的时候让Service保持  

#### 1.3.1.2 Service和Thread区别

Thread是程序执行最小单元，分配CPU的基本单位  

Service在主线程运行不能做耗时操作，轻量级的activity通信，activity无法在销毁后管理子线程  

在Service运行耗时操作需要开启Thread  

应用：数据统计，播放音乐  

### 1.3.2 开启service的两种方式以及区别

#### 1.3.2.1 startService  

1 定义一个类继承service  
2 在mainfest.xml文件中配置该service  
3 实用Context的startService(Intent)方法启动该service   
4 不再使用时，调用stopService(Intent)方法停止该服务  

#### 1.3.2.2 bindService 绑定  

1 创建bindService服务端（service），继承自Service并在类中，创建一个实现IBinder接口的实例对象并提供公共方法给客户端（activity）调用  
2 从onBind()回调方法返回此Binder实例  
3 在客户端（activity）中，从onServiceConnectde()回调方法接受Binder，并实用提供的方法调用绑定服务  

<span id = "jump1.4">

## 1.4 Broadcast Receiver 广播

</span>

### 1.4.1 广播

广播：应用程序（不同APP）之间传递信息的机制，类似于Java中的观察者模式，广播内容时一个Intent，这个Intent可以携带我们要传送的数据  
使用场景：同一个APP中具有多个进程的不同组件之间的消息通信；  
不同APP中具有多个进程的不同组件之间的消息通信；  
种类：  
1 Normal Broadcast: Context.sendBroadcast 普通广播  
2 System Broadcast: Context.sendOrderedBroadcast 有序广播（系统广播）  
3 Local Broadcast: 只在自身APP内传播    

### 1.4.2 实现广播-receiver

1 静态注册：写在mainfest.xml文件中，注册完成就一直运行，进程杀死（APP销毁）后仍然能接受广播  
2 动态注册：跟随activity的生命周期，注意在onDestroy（）中回收，否则引起内存泄漏  

### 1.4.3 广播实现机制

1 自定义广播接收者BroadcastReceiver，并复写onRecvice()方法；  
2 通过Binder机制向AMS（Activity Manager Service）进行注册；  
3 广播发送者通过Binder机制向AMS发送广播；  
4 AMS查找符合相应条件（Inetnt/Permission等）的BroadcastReceiver，将广播发送到BroadcastReceiver（一般情况下时Activity）相应的消息循环队列中；  
5 消息循环执行拿到此广播，回调BroadcastReceiver中的onReceive()方法。  

### 1.4.4 LocalBroadcastManager详解

本地广播：数据安全，其他APP无法发送广播，比全局广播更加高效  
通过Handler实现，它的sendBroadcast()方法世纪时通过Handler发送一个Messgge实现的  
相比系统广播通过Binder实现更高效，同时因为使用Handler实现，所以别的应用无法向我们的应用发送广播，而我们的广播也不会离开我们的应用  
内部协作主要靠两个Map集合：mReceivers和mActions,还有一个List集合mPendingBroadcasts存储待接收的广播对象  

<span id = "jump1.5">

## 1.5 ++Binder跨进程通讯++

</span>


通常意义下，binder指的是一种通讯机制（跨进程），binder值得是代理对象

### 1.5.1 Linux内核有关Binder的基础知识

进程隔离/虚拟地址空间  

系统调用  

binder驱动  

### 1.5.2 Binder通讯机制介绍

#### 1.5.2.1 为什么使用binder

1 android实用linux内核拥有非常多的夸进程通信机制(管道，Socket等)  
2 性能  
3 安全(双方校验，不同于Socket的客户端可以手动填写IP)  

#### 1.5.2.2 binder通信模型 

1 操作：binder驱动   
2 电话基站以及通讯录：serviceManager   

#### 1.5.2.3 binder通信机制原理

代理，对象转换，对应数据结构

### 1.5.3 Aidl

判断是否同进程


<span id = "jump1.6">

## 1.6 WebView安全漏洞

</span>


加载网页  

### 1.6.1 Webview常见的一些坑  

1 Android API 16级一起的版本存在远程代码执行安全漏洞，没有正确先知实用WebView.addJavascriptInterface方法，远程攻击者可通过实用Java Reflection API(反射机制) 利用该漏洞执行仁义Java对象的方法  
2 webview在布局文件中实用：wedview写在其他容器中时（先在onDestroy()中销毁，然后在调用webview的销毁，才能真正销毁，不会引起内存泄漏）  
3 jsbridge 桥，让本地调用远端代码，或远端调用本地代码  
4 webviewClient.onPageFinished(加载完成的回调，加载各种网页时最好用后边的方法) -> WebChromeClient.onProgerssChanged()  
5 后台耗电 会自己开启线程，在activity的onDestroy()中销毁
6 webview硬件加速导致页面渲染问题（界面闪烁，设置暂时关闭加速）  

### 1.6.2 关于webview的内存泄漏问题

1 独立进程，简单暴力，不过可能涉及到进程间通讯  
2 动态添加webview，对传入webview中使用的context实用弱引用，动态添加webview意思在布局创建viewgroup用来放置webview，activity创建时add进来，activity停止时remove掉  

---

<span id = "jump2">

# 二 异步消息处理机制

</span>

<span id = "jump2.1">

## 2.1 ++Handler++

</span>


### 2.1.1 什么是Handler

Android消息机制的一个上层接口，通过发送和处理Message和Runnable对象来关联相对应线程的messagequeue  
1 可以让对应的Message和Runnable在未来的某个时间点进行相应处理（子线程做完操作完成后通知主线程）  
2 让自己想要处理的耗时操作放在子线程，让更新UI的操作放在主线程  

### 2.1.2 handler的使用方法

1 post(runnable):使runnable在主线程  
2 sendMessage(message):传递编号，去handler实现  

### 2.1.3 handler机制的原理

hander通过Looper（死循环不停去获取消息，内部源码通过hander传递消息）在MessageQueue中读取消息，发送给hander来处理

### 2.1.4 handler引起的内存泄漏以及解决方法

1 handler内部持有外部activity的弱引用，并把handler设为静态内部类  
2 在activity的onDestroy中调用handler.removeCallback()  

<span id = "jump2.2">

## 2.2 IntentService

</span>


### 2.2.1 IntentService是什么

继承自Service优先级比Service高，内部封装了HandlerThread和Handler  
继承并处理异步请求的一个类，内部有一个工作线程来处理耗时操作，启动方式和传统Service一样，同时，当执行完任务后会自动停止，不需要手动控制会stopSelf()。另外可以启动多次，而每一个耗时操作会以工作队列的方式在onHandleIntent回调方法中执行，并切每次只会执行一个工作线程，执行完第一个在执行第二个

### 2.2.2 IntentService使用方法

创建时只需要实现onHandleIntent和构造方法就可以，onHandleIntent为异步方法，可以执行耗时操作

### 2.2.3 IntentService源码解析

调用stopSelf()传入参数，所有消息处理完毕后才会终止任务  

<span id = "jump2.3">

## 2.3 AsyncTask

</span>


### 2.3.1 什么是AsyncTask

轻量级异步类，本质上就是一个封装了线程池和handler的异步框架，只能做耗时比较短的操作，耗时较长的操作用线程池来做

### 2.3.2 AsyncTask的使用方法

#### 2.3.2.1 三个参数

AsyncTask<Intager,Intager,String>  
执行是所要传入的参数，进度，执行完毕后对结果执行返回

#### 2.3.2.2 五个方法

onPreExecute()开始执行异步线程，初始化操作，在UI线程中调用的，经常用来显示进度条  
doInBackground()耗时操作,结果会返回到onPostExecute()，可以在方法中调用publishProgress()返回进度  
publishProgress()返回进度   
onProgressUpdate()会在doInBackground()中的publishProgress()调用后被调用  
onPostExecute()执行结束返回第三个参数    

### 2.3.3 AsyncTask内部原理

对线程和线程池的封装，本质是一个静态线程池，会派生出子类实现不同的异步任务，这些任务都是提交到静态线程池中执行  
线程池中工作线程执行doInBackground()方法执行异步任务  
当任务状态改变后，工作线程向UI线程发送消息，内部的InternalHandler响应这些消息，并调用相关回调  

### 2.3.4 AsyncTask的注意事项

1 内存泄漏：handler内部持有外部activity的弱引用，并把handler设为静态内部类在activity的onDestroy中调用AsyncTask的销毁   
2 生命周期：不会随着activity销毁  
3 结果丢失：屏幕旋转，activity重新创建  
4 并行or串行：建议只使用串行，并行效率高但是不稳定  

<span id = "jump2.4">

## 2.4 HandlerThread

</span>


### 2.4.1 HandlerThread是什么

开启Thread进行耗时操作，而多次创建和销毁线程很耗系统资源  
循环线程框架handler+thread+looper  
特点：本质是一个线程类，继承了Thread，内部有looper对象，可以进行looper循环，通过获取HandlerThread的looper对象传递给Handler对象，可以在handleMessage方法中执行异步任务，不会有堵塞，减少对性能的消耗，缺点是不能进行多任务，串行队列只有一个线程，不同于线程池，不能并发  

### 2.4.2 HandlerThread源码解析

可以创建Handler类，自己持有以阻塞的方式创建的looper对象，指定优先级解决内存泄漏，必要时可重写looper，syschronized锁住looper保证不会同时执行

---

<span id = "jump3">

# 三 View相关技术

</span>


<span id = "jump3.1">

## 3.1 View的渲染和绘制

</span>

### 3.1.1 view树的绘制流程

#### 3.1.1.1 measure -> layout -> draw  

是否重新计算视图大小，是否重新安置视图位置，重绘  

#### 3.1.1.2 *measure自上而下的进行遍历,从父视图开始  

1 ViewGroup.LayoutParams：指定视图高度和宽度  

2 MeasureSpec:测量规格(32位int值，最高的两位模式占位符，后面30位测量规格的大小)  

重要方法： measure,onMeasure(),setMeasuredDimension();

#### 3.1.1.3 layout

自上而下的进行遍历,从父视图开始 

#### 3.1.1.4 draw 

1 invalidte():调用的时候如果大小没有发生变化不会调用layout   
2 requestLayout()：当布局发生变化调用measure和layout，不会调用draw方法


<span id = "jump3.2">

## 3.2 ++事件分发++

</span>

### 3.2.1 为什么会有事件分发机制

由于Android中view是树形结构，view可能会重叠在一起，当我们点击的地方有多个view都可以响应的时候，事件分发机制判断点击事件分给哪个view  
Phonewindow:抽象类window的实现，view的实际管理容器  
DecorView:Phonewindow的内部类，进行消息传递

### 3.2.2 三个重要的事件分发的方法

avtivity无法拦截事件，因为会导致整个屏幕失去响应；而view作为事件传递末端没必要  

1 dispatchTouchEvent：决定是否分发给子view  

2 onInterceptTouchEcent：用来拦截事件  

3 onTouchEvent：处理事件  

### 3.2.3 事件分发的流程

Activity -> PhoneWindow -> DecorView -> ViewGroup -> ... -> View  
如果最末端view没有事件会以此回传直道Activity才会被废弃  

<span id = "jump3.3">

## 3.3 ++ListView++

</span>

### 3.3.1 什么时listview

一个能用数据集合以动态滚动的方式展示到用户界面上的view

### 3.3.2 listview适配器模式

adapter负责为每个数据制作view交给listview来显示，保证数据和view分离（MVC设计模式）  

### 3.3.3 listview的recycleBin机制

垃圾回收机制，把划出屏幕的item放入recycleBin，保证只有在屏幕上显示的元素在内存中

### 3.3.4 listview的优化

1 convertview重用/viewHolder  
判断convertview是否为空创建相应的View  
viewHolder模式避免多次findviewByid  
2 三级缓存/监听滑动事件  
图片加载的时候用到图片缓存机制  
getView少做耗时操作，设置listview监听滑动事件滑动停止再做耗时操作  
绘制中避免半透明元素，半透明比不透明更耗时

---

<span id = "jump4">

# 四 Android构建

</span>


<span id = "jump4.1">

## 4.1 android编译打包

</span>

通过aapt将资源文件打包R.java -> 通过aidl将.aidl打包成JavaInterface -> 两者加上其他Java文件  
.java -> .class字节码 -> .dex -> .apk -> 签名 -> 对齐操作   

jenkins持续集成构件

<span id = "jump4.2">

## 4.2 Proguard混淆

</span>

### 4.2.1 Proguard到底是什么

在Android打包时压缩，优化，混淆我们的代码，主作用可以移除代码中的无用类，字段，方法和属性同时可以混淆

### 4.2.2 Proguard技术的功能

1 压缩：检查并移除代码中的无用类，字段，方法和属性  
2 优化：字节码文件进行优化，移除无用的字节码  
3 混淆：把有意义的名词变成没意义的名词  
4 预检测：处理后的代码再次检测  

### 4.2.3 Proguard工作原理

EntryPoint:Proguard过程中不会被处理的类或方法，未使用的类会被自动丢弃

<span id = "jump4.3">

## 4.3 Git

</span>

### 4.3.1 git容易混淆的两个概念

工作区：文件目录  
gitignore文件：不想上传的文件在这里配置  

### 4.3.2 一些常用的git命令

git--help 获取git命令  

git init     创建git仓库  
git status   查看当前仓库状态  
git diff     +文件名查看修改的内容  
git add      +文件名，放到暂存区  
git commit   暂存区提交到代码区  
git clone    +git地址 去远程仓库克隆一份  
git branch   查看当前分支  
git checkout 切换分支

### 4.3.3 git的两种工作流

1 fork/clone:复制到自己的远程仓库/复制到本地仓库(保证代码开发质量)  
- 1 Fork   从项目的远程仓库复制到自己的远程仓库
- 2 Clone  从自己的远程仓库复制到本地仓库
- 3 Update a file 更新文件放到自己本地的缓存区(git add)
- 4 Commit 暂存区提交到代码区(本地) 
- 5 Push   本地提交到自己的远程仓库
- 6 Pull request  从自己的远程仓库提交到项目的远程仓库(管理员核实后合并到项目仓库)

2 clone  项目远程仓库和自己本地直连，缺少管理员

<span id = "jump4.4">

## 4.4 Gradle语法

</span>

### 4.4.1 settings.gradle:

多模块开发定制规则  

### 4.4.2 buildi.gradle(项目):

buildscript:repositories依赖jar包或代码，dependencies依赖包
allproject：声明模块属性  

### 4.4.3 buildi.gradle(模块):  
可覆盖顶层buildi.gradle的任何属性  
apply plugin：谷歌官方提供  
android：  
compileSdkVersion用来编译的版本号，buildToolsVersion构建的版本号  
defaultConfig是可以覆盖mainfest的属性核心属性，applicationId覆盖mainfest的包名，minSdkVersion最低api，targetSdkVersion稳定版本不用向前兼容  
buildTypes：是否混淆的配置  
dependencies：当前模块所依赖的一些包。点击上边工具栏的Project Structure,点击Dependencles,点击加号Library dependency搜索需要的第三方包


---

<span id = "jump5">

# 五 开源框架源码

</span>


<span id = "jump5.1">

## 5.1 Okhttp网络框架

</span>

### 5.1.1 okhttp使用简介

1 创建OkhttpClient对象  
2 创建Request对象，通过内部Builder调用生成Request对象  
3 通过OkhttpClient.newcall创建call对象（okhttp3.Response）（execute/enqueue,同步／异步）  

### 5.1.2 okhttp源码剖析

分层，把复杂任务拆分  
getResponseWithInterceptorChain()中包含所有拦截器Interceptor  


<span id = "jump5.2">

## 5.2 Retrofit网络框架

</span>

底层用的也是okhttp

### 5.2.1 Retrofit使用简介

1 在Retrofit中通过一个接口作为http请求的API接口  
2 创建一个Retrofit实例  
3 通过Retrofit实例调用API接口

### 5.2.2 Retrofit源码剖析

接口形式，动态代理   

1 首先，通过method把它转换成ServiceMethod；  
2 通过ServiceMethod，args获取到okhttpcall对象  
3 再把okhttpcall进一步封装病返回call对象  

<span id = "jump5.3">

## 5.3 Volley网络框架

</span>

适合数据量小而频繁

### 5.3.1 Volley使用简介

1 获取RequestQueue对象（请求队列，适合并发，不用多次创建），通过volley.newRequestQueue  
2 创建StringRequest对象  
3 将StringRequest对象add到RequestQueue对象

### 5.3.2 Volley源码剖析

线程缓存队列，先判断缓存再判断是否过期再请求网络

<span id = "jump5.4">

## 5.4 butterknife注解框架

</span>

### 5.4.1 butterknife使用简介

依托java的注解机制来实现辅助代码生成的框架,不是反射机制所以不能private或static  


```
Butterknife.bind(this);

```

1 绑定view  @BindView(R.id.xxx)  view不能private或static

findViewBayId  

2 给view添加点击事件  @onClick(R.id.xxx) 或 @onClick(R.id.xxx,R.id.xxxx)  

3 给listview setItemonClickLisener  @onItemClick(R.id.xxx)

### 5.4.2 butterknife源码剖析

1 扫描java代码中的所有butterknife注解  
2 ButterKnifeProcessor -><className>$$ViewBinder  生成java类  
3 调用bind方法加载生成ViewBinder类，动态注入view属性

<span id = "jump5.5">

## 5.5 Glide图片框架

</span>

### 5.5.1 Glide使用简介

Glide.with(context).load(url).into(imageview)

### 5.5.2 Glide源码剖析

通过looper判断是否主线程

---

<span id = "jump6">

# 六 Android异常与性能优化

</span>


<span id = "jump6.1">

## 6.1 ANR

</span>

### 6.1.1 什么是ANR

Android Not Responding  
如果应用有一段时间点击不够灵敏，系统就会抛出（等待或关闭）  
超过5秒，广播是10秒无响应，

### 6.1.2 造成ANR的主要原因

应用响应性由Activity Manager 和 WindowManager系统服务监视  

主线程做了耗时操作（4.0后网络IO不允许在主线程中）  

Activity所有生命周期回掉，Service，广播的onReceive回调，没有子线程的looper的Handler的handleMessage,post(Runnable)，AsyncTask的回掉中出了doInBackground都是执行在主线程的  

### 6.1.3 如何解决ANR

在Asynctask处理耗时IO操作  

使用Thread或者HandlerTgerad提高优先级  

利用Handler转移到子线程  

Activity的回掉方法中尽量避免耗时操作  

<span id = "jump6.2">

## 6.2 OOM

</span>

### 6.2.1 什么是OOM

Out Of Memory 内存溢出  
当前占用内存加上我们申请的内存资源超过Dalvik虚拟机的最大内存限制  

### 6.2.2 一些容易混淆的概念

内存溢出／内存抖动／内存泄漏  
OOM／段时间内大量对象被创建又被马上释放，会触发.gc／进程中的某些对象已经没有引用了，但是可以引用到其他没有被回收的对象导致gc无法产生作用

### 6.2.3 如何解决OOM

Bitmap优化  
1 图片显示（比如listview滑动时不要调网络请求）  
2 及时释放内存（由于Bitmap是由JNI实现的，内存中分java区和C区，java区可以自己回收，而C区不可以）  
3 图片压缩（防止一张大图直接造成OOM）  
4 使用inBitmap属性（复用内存区域）  
5 捕获异常  
其他  
listview：convertview/lru（三级缓存机制）
避免在onDraw方法中执行对象创建  
谨慎使用多进程  

<span id = "jump6.3">

## 6.3 Bitmap

</span>

### 6.3.1 recycle()

回收内存（C区），不可逆，谨慎操作  

### 6.3.2 lru

LRU算法：清除最近最少使用的对象出缓存队列（利用trimToSize方法）  

### 6.3.3 计算inSampleSize

BitmapUtils，传入原始长宽自动算出合适的长宽比例加载到内存中

### 6.3.4 缩略图

thumbnail方法

### 6.3.5 三级缓存

网络／本地／内存  
首次打开走网络，保存到本地和内存各一份  
内存缓存最悠闲，速度更快，本地在内存被清理时加载


<span id = "jump6.4">

## 6.4 UI卡顿

</span>

### 6.4.1 UI卡顿的原理

60fps -> 16ms  
android系统每隔16毫秒对UI进行渲染，如果每次都渲染成功就可以达到60fps,即每秒60帧  
意味着程序大多数操作需要在16ms内完成  

overdraw  过度绘制  屏幕上某个像素在同一帧被绘制了多次，经常出现在多层次的UI结构中  
利用开发者工具中的过度绘制选项，移除非必要的背景图  

### 6.4.2 UI卡顿原因分析

1 人为的在UI线程中做轻微的耗时操作，导致的UI线程卡顿（开启子线程）；  
2 布局Layout太过复杂无法在16毫秒内完成渲染（和UI沟通界面背景）   
3 同一时间动画执行的次数过多，导致CPU和GPU负载过重  
4 过度绘制  屏幕上某个像素在同一帧被绘制了多次   
5 view频繁的触发measure\layout（测量，绘制，摆放）,导致measure\layout累计耗时过多及整个view频繁的重新渲染   
6 内存频繁的GC过多（回收不必要的内存，引起内存抖动），导致暂时阻塞渲染操作  
7 冗余资源及逻辑等导致加载和执行缓慢（代码优化）  
8 ANR（UI卡顿就是轻量版的ANR）

### 6.4.3 UI卡顿总结

1 布局优化 ：尽量不存在冗余嵌套，以及过于复杂的布局；（include）  
2 列表及adapter优化：listview滑动结束后再绘制及加载图片资源  
3 背景和图片等内存分配优化：减少不必要的背景  
4 避免ANR（常用异步处理框架）

<span id = "jump6.5">

## 6.5 内存泄漏

</span>

### 6.5.1 java内存泄露基础知识  

#### 6.5.1.1 java内存管理策略

1 静态存储区：存放静态变量  
2 栈区：在函数中定义的基本类型变量和对象的引用变量  
3 堆区（动态）：由new创建的对象和数组以及对象的实例变量 在函数（代码块）中定义一个变量时，java就在栈中为这个变量分配内存空间，当超过变量的作用域后，java会自动释放掉为该变量所分配的内存空间；在堆中分配的内存由java虚拟机的自动垃圾回收器来管理    

#### 6.5.1.2 java室如何管理内存的

Java的内存管理就是对象的分配和释放问题。  
程序员用new申请空间  
释放由GC管理（跟顶点不可达的对象会被回收）

#### 6.5.1.3 java中的内存泄漏

该被释放的对象没有或得不到释放造成的内存空间浪费叫做内存泄漏  

### 6.5.2 android内存泄漏  

1 单例：静态特性使它的生命周期和app一样，不要在单例中持有activity的context，应该使用application的context  
2 匿名内部类：内部类会持有外部类的引用，存在静态方法的内部类会引起外部类无法回收，需要声明成静态内部类  
3 handler：和内部类相同会持有外部类引用，可以创建一个静态的内部类，让其持有外部类的弱引用  
4 尽量避免使用static变量：造成无法回收，尽量利用懒加载，无法避免要管理生命周期  
5 资源未关闭：及时关闭资源  
6 AsyncTask：和handler一样，但是可以在onDestroy释放  

<span id = "jump6.6">

## 6.6 内存管理

</span>

### 6.6.1 内存管理机制概述

1 分配机制：操作系统会为每个进程分配合理的内存  
2 回收机制：内存不足时，系统会合理的回收和再分配内存  

### 6.6.2 android内存管理机制

1 分配机制：弹性分配（初始根据硬件大小）  
2 回收机制：尽可能多的存在进程保证应用启动速度，根据进程优先级（不推荐直接退出应用，以达到热启动）  

### 6.6.3 内存管理机制目标

1 更少的占用内存  
2 在合适的时候，合理的释放系统资源  
3 在系统内存紧张的情况下，能够释放掉大部分不重要的资源，为系统提供可用内存  
4 能够合理的在生命周期中，保存或还原重要数据，让系统可以正确的恢复应用  

### 6.6.4 内存优化的一些方法

1 Service完成任务后，尽量停止它；尽量使用intentService，会自动关闭  
2 UI不可见的时候，释放掉只有UI使用的资源  
3 系统内存紧张时，尽可能多的释放一些非重要资源  
4 避免滥用bitmap导致内存浪费，别忘记释放c区内存  
5 使用针对内存优化过的数据容器，尽量少用枚举常量  
6 避免使用以来注入框架  
7 使用ZIP对齐的APK  
8 使用多进程（不能滥用，数据传输和安全问题）  

<span id = "jump6.7">

## 6.7 冷启动优化

</span>

冷启动就是在系统中没有该应用的任何进程的时候的启动，对应热启动的之前存在于后台  

冷启动时间从应用启动创建进程开始计算，到完成视图的第一次绘制，即activity内容对用户可见为止  

流程：Zygote进程中fork创建出一个新的进程，创建和初始化application类，创建mainactivity类，inflate布局当onCreate/onStart/onResume走完，contentview的measure／layout／draw显示在界面上  

application的构造->attachBaseContext()->onCreate()->activity的构造->onCreate()->配置主题中的背景等属性->onStart()->onResume()->测量布局绘制现实在界面上  

优化：  
1 减少onCreate()方法的工作量  
2 不让application参与业务操作  
3 不要在application进行耗时操作  
4 不要以静态变量的方式在application中保存数据  
5 布局／mainThread  按需加载／利用子线程

<span id = "jump6.8">

## 6.8 其他优化

</span>

1 android不要用静态变量存储数据：应用进程不安全，静态变量等数据会由于进程被杀死而被初始化  
利用 文件／SP／contentProvider。。。注意非空判断    
2 有关SP的安全问题：每个进程自己维护一个副本，不能跨进程同步，会在应用结束后合并；永远存在内存中，是文件过大问题会导致内存OOM   
3 内存对象序列化；序列化就是将对象转换为可以存储或传输的形式的过程  
Serializeble：java提供，会产生大量临时变量造成内存抖动，适用于磁盘中的数据  
Parcelable：android提供，性能高，不能使用磁盘上存储的数据，适用于内存中的数据  
4 避免在UI线程中做繁重的操作：多使用异步操作，利用开元框架BlockCanary监视，以修改  


---
<span id = "jump7">

# 七 热门前沿知识

</span>

<span id = "jump7.1">

## 7.1 MVC架构设计模式

</span>

Model-View-Controller  

界面被分到了View（xml），数据分到了载体Model上由Model“携带”，业务集中在Controller（activity）中，而推动业务的事件由用户与View交互，通过View向Controller发动。


<span id = "jump7.2">

## 7.2 MVP架构设计模式

</span>

Model-View-Presenter  

由于安卓xml量级太小，将activity分配到view，利用接口交互  

切断的View和Model的联系，让View（activity和xml）只和Presenter（原Controller）交互，减少在需求变化中需要维护的对象的数量。

<span id = "jump7.3">

## 7.3 MVVM架构设计模式

</span>

Model-View-ViewModel  

ViewModel大致上就是MVP的Presenter和MVC的Controller了，而View和ViewModel间没有了MVP的界面接口，而是直接交互，用数据“绑定”的形式让数据更新的事件不需要开发人员手动去编写特殊用例，而是自动地双向同步。数据绑定你可以认为是Observer模式或者是Publish/Subscribe模式，原理都是为了用一种统一的集中的方式实现频繁需要被实现的数据更新问题。


<span id = "jump7.4">

## 7.4 android插件化

</span>

由于65536的瓶颈导致提出android插件化，对应h5的模式性能较好，由于利用反射机制本身也存在性能问题   

1 动态加载APK  
利用类加载器：将字节码添加到虚拟机 DexClassLoader／PathClassLoader，前者可以动态加载dex／jar，后者只能加载文件目录下的apk
2 资源加载:利用反射   
3 代码加载:利用反射获取生命周期并绑定  

<span id = "jump7.5">

## 7.5 android热更新

</span>

测试不充分和开发不严谨造成的崩溃的紧急修复的方案  

流程：  
线上检测到严重的crash，拉出bugfix分支并在分枝上修复问题，测试后利用Jenkins构建和补丁生成，app推送或主动拉取补丁，将bugfix代码合并到master上  

框架：  
1.Dexposed阿里巴巴提供，功能丰富，性能一般；  
2.AndFix阿里巴巴提供，纯粹热更新，性能更好；  
3.Nuwa dex分包原理

原理：  
DexClassLoader，动态加载dex，jar
ClassLoader遍历数组，新的dex排序在前面优先加载  

<span id = "jump7.6">

## 7.6 进程保活

</span>

1 利用系统广播拉活：不可控性  
2 利用系统sevice机制：不可控性  
3 利用native进程：5.0后废弃  
4 利用JobScheduler机制：5.0后新增  
5 利用账号同步机制：新版本废弃  


---
<span id = "jump8">

# 八 Java高级技术

</span>

<span id = "jump8.1">

## 8.1 IO相关-Socket

</span>

### 8.1.1 java网络编程

#### 8.1.1.1 基础知识

1 IP地址／端口号：用来识别网络中的通讯实体／通讯实体的通讯程序  
2 tcp/udp协议：可靠传输协议顺序无差错的数据流／无连接协议，时间和准确性不可靠（视频通讯类）  
3 URL：统一的资源定位器，指向互联网中的资源，从字节输入流接收数据，到输出流写入文件  
4 intAddress：IP地址类  

#### 8.1.1.2 socket

1 创建socket实例  
完成计算机之间相互通讯的抽象功能，我们使用的室TCP IP协议的socket   
2 客户端链接  
创建客户端socket，并指定服务器地址和端口号；  
获取输出流，向服务器发送信息；  
获取输入流，并读取服务器的相应信息；  
关闭响应资源；  
3 服务端链接   
创建ServerSocket对象，绑定监听端口  
创建socket对象通过accept（）阻塞方法监听客户端请求，等待链接  
连接建立后，通过输入流读取客户端发送的请求  
通过输出流向客户端发送信息  
关闭相关资源  

<span id = "jump8.2">

## 8.2 IO相关-BIO／NIO

</span>

### 8.2.1 阻塞IO又叫BIO

#### 8.2.1.1 java的I／O接口

1 基于字节操作的I／O接口  
2 基于字符操作的I／O接口  
3 基于磁盘操作的I／O接口  
4 基于网络操作的I／O接口：我们用的  

#### 8.2.1.2 阻塞IO的通讯模型

数据到来时才会返回  
客户端很多时创建大量线程，阻塞带来频繁的无意义上下文切换  


### 8.2.2 NIO

线程阻塞会失去CPU调用权，BIO数据写入OutputStream或从InputStream读取时，随时可能会阻塞   
当前一些需要大量HTTP长链接的情况下（淘宝，IM等），服务端同时保持百万级线程很难通过线程优先级解决  
需要另外一种新的IO操作方式NIO  

#### 8.2.2.1 工作原理

专门的线程处理IO事件，同时是事件驱动机制，事件到来的时候触发，而不是同步监视时间，有线程见通讯方式（wait／notify方式）进行通讯，保证每次上下文切换都是有意义的  

#### 8.2.2.2 通讯模型

双向通道，而不是传统的单向流，在通道上注册事件，双端各自维护一个管理通道的对象Selector，Selector对象可以检测到注册的事件，其他时候处理线程一直阻塞

#### 8.2.2.3 实例

客户端：  
获取一个Socket通道，通过SocketChannel.open()获取  
设置通道非阻塞  
获得一个通道管理器，Selector.open()  
客户端链接服务器，其方法执行并没有实现链接，需要listen()方法中调用channel.finishConnect()才能完成链接  
将通道管理器和该通道绑定，并为该通道注册SelectionKey.OP_CONNCT事件  
采用轮询循环的方式监听Selector上是否有监听的事件，有则处理，并删除SelectionKey防止重复处理  

服务端：  
获取一个ServerSocket通道，通过ServerSocketChannel.open()获取  
设置通道非阻塞  
将该通道对应的ServerSocket绑定到port端口
获得一个通道管理器，Selector.open()  
将通道管理器和该通道绑定，并为该通道注册SelectionKey.OP_CONNCT事件，当事件到达时处理，没有事件会一直阻塞   
采用轮询循环的方式监听Selector上是否有监听的事件，有则处理，并删除SelectionKey防止重复处理  


<span id = "jump8.3">

## 8.3 多线程相关

</span>

### 8.3.1 多线程创建

1 thread／runnable：  
继承thread类，实现runnable接口，start／实现runnable接口，创建runnable对象，作为参数传入thread，start  
2 两种启动线程方法的区别：  
java中是单继承，集成thread类就不能继承别的了，为了弥补这点使用runnable接口，比即成更灵活，可以用一个实例创建多个线程  
3 start方法和run方法的区别：  
开启线程的方法，就绪状态／线程体，执行结束线程就结束了  

### 8.3.2 线程间通信

synchronized关键字：  
synchronized对象锁，锁住当前对象  
对象锁锁住当前对象，来实现线程间通信，（共享内存式通信：多个线程持有同一个对象，线程1执行完之后线程2才会执行，比较笨重） 
synchronized／volatile  
线程有自己的内存空间，造成数据不同步，volatile关键字读取主内存的数据



