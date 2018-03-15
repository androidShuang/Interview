#Android基础
####1.四大组件是什么
Activity,Broadcast receiver,Service,Content Provider

####2.四大组件的生命周期和简单用法
acitivity生命周期
1.oncreate
2.onstart
3.onresume
4.onpause
5.onstop
6.onDestroy
附一张图片
![activity生命周期](http://img.blog.csdn.net/20160717151833576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
简单阐述一下各个生命周期需要做的事情：
#####activity生命周期
1.onCreate:当Activity被创建时，系统自动调用onCreate()方法进行一些初始化操作，比如创建views，设置数据到list等。该方法提供了一个Bundle类型的变量，该变量中有这个activity以前的状态信息，前提是以前存过这些信息。这个方法执行完后执行的是onStart()方法;若在onCreate方法中加入finish()方法，onCreate下一个运行onDestroy方法。这是官方SDK上的说明，onCreate方法是整个生命周期中唯一必须实现的方法。
2.onStart:当activity对用户可见时会调用onStart，当activity在前台显示时，会运行onResume;当activity还没在前台显示就被隐藏(停止状态)了会运行onStop()，比如在onStart方法中用了finish()方法的话，onStart()之后就会直接运行onStop->onDestroy。当进入onStart状态时，Activity将可见但是不能交互。可以把onStart和onStop看成一对。
3.onResume:当activity开始与用户交互时会调用onResume，并且为了用户操作此时该activity位于activity栈的顶部。经过某些操作后该方法执行完后执行的是onPause()。当Activity被一个透明或者Dialog的Activity覆盖时将处于Pause状态，它仍然可见，但是已经失去了焦点，不可与用户交互。通过调用onResume()方法使Activity从Pause恢复为Active状态。例如在一个Dialog中保存数据，并刷新Acitivity的数据，则需要在onResume()方法中进行数据查询并显示.
4.onPause:当一个activity运行到onResume方法后，不管是这个activity要销毁还是要暂停或停止，都会调用该方法。这个方法之后有可能是onResume也有可能是onStop，若是在这个activity-A中打开一个不完全覆盖这个activity-A的新activity-B，那么activity-A就会是onPause状态，当activity-B退出时，activity-A就直接运行onResume(前提是onPause的方法执行完了，否则会等onPause方法执行完后才运行onResume方法，所以不建议在这个方法中执行CPU密集的操作)。若是需要退出activity-A，那么下一个就会执行onStop。onPause()用于提交未保存发生变化了的持久化数据，及停止动画及其他其他比较消耗CPU的事件(比如广播接收器，传感器(比如GPS)，或者消耗电量的资源)，这是为了更好的运行新的activity。在onPause方法中可以保存一些状态信息，用于恢复时使用，例如输入框中已经输入的内容，在这个方法中保存。当Acivity恢复时，可以在onRestart()或者onStart()方法中将数据恢复。
5.onStop:当这个activity完全看不见的时候，会调用onStop方法，因为另一个activity会调用onResume并且覆盖这个activity。以下三种情况都会使这个activity调用onStop()方法，第一种是一个新的activity被执行，第二种是一个已经存在的activity被切换到最前端，第三种是这个activity要被销毁。如果通过用户召回这个activity，那么会调用onRestart方法;若这个activity要被销毁，则调用onDestroy方法。当用户自己退出程序的时候，建议在onStop方法中保存数据。
6.onDestory:当activity销毁前会调用该方法，比如发生如下情况：activity调用了finish()方法来结束这个activity，或者因为系统为了节省空间而临时销毁这个activity，这两个情况可以通过isFinishing()方法判断。
7.onRestart:当Activitiy被停止时，调用这个方法进行唤醒，该方法优先于onStart()方法。若在onRestart()方法中加入finish()语句，则还是会继续运行onStart及后面的状态方法直到onDestroy运行完。onRestart（）是activity的一个生命周期，基类中就只做了一件事情就是把mCalled设置为true代表界面当前使用状态。其他的操作都是在activitymanagerservice中去执行的，service中执行的代码也不是说属于执行这个生命周期的方法，它的意思是服务将这个界面从堆栈中拿出来重新放到前台可见的时候允许客户端程序做一个自定义事情的回调方法。你需要先理解activity的生命周期的含义，它只是服务端控制activity状态时允许客户端activity在相对应的时刻添加一些自己要做的事情的回调方法而已。onRestart 当处于非栈顶状态的活动需要再次返回栈顶，展现给用户的时候，触发该方法。也就是说执行了onStop()且没有执行onDestroy()的Activity被重新激活时，就会调用onRestart()方法。生命周期只有一种：Running状态->onPause()->onStop()->不可见状态->onRestart()->onStart->onResume()->获得焦点可交互状态。onRestart()方法一般是进行一些数据的初始化操作。onStop方法会将Activity中的资源释放，所以需要在onRestart方法中重新初始化。但是这些资源的申请操作一般在onStart方法中进行，onRestart()方法并不常用。
#####service生命周期
上图
![service生命周期](https://upload-images.jianshu.io/upload_images/944365-cf5c1a9d2dddaaca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/456)
回调图
![回调图](https://github.com/HIT-Alibaba/interview/blob/master/img/android-service-callback.png?raw=true)

4个手动调用的方法  

| 手动调用方法 | 作用 |
|--------|--------|
|    startService()    |    启动服务    |
|    stopService()    |    关闭服务    |
|    bindService()    |    绑定服务    |
|    unbindService()    |    解绑服务    |

5个自动调用的方法  

| 内部自动调用的方法 | 作用 |
|--------|--------|
|    onCreate()    |    创建服务    |
|    onStartCommand()    |    开始服务    |
|    onDestroy()    |    销毁服务    |
|    onBind()    |    绑定服务    |
|    onUnbind()    |    解绑服务    |

服务的生命周期根据他的启动方式不同，而有所区别
1.start方式：onCreate()，onStartCommand()。onDestroy释放资源。
2.bind方式： onCreate()，onBind()方法。需要所有client全部调用unbindService()才能将Service释放资源，等待系统回收。
下面通过一道练习题在认识一下
start=startService
stop=stopService
bind=bindService
unbind=unbindService  
	
| 顺序 | 答案 |
|--------|--------|
|   start->stop    |    A-B-C    |
|    bind->unbind    |    A-D-E-C    |
|    start->start    |    A-B-B    |
|   start->bind->stop   |    A-B-D    |
|    start->bind->unbind    |    A-B-D-E    |
|    bind->start->unbind    |    A-D-B-E    |

A:onCreate()
B:onStartCommand()
C:onDestroy()
D:onBind()
E:onUnbind()
**问题：start->bind->unbind 是不是A-B-D-E-C bind->start->unbind是不是A-D-B-E-C，在执行unbind的时候会调用unbind->ondestroy？
unbindService执行后，它会去检查是否执行过startService，如果执行过，它只是被解绑，解绑后并不自动调用onDestroy，如果想让它destroy，需要再加一步stopService，可以用Log方法检查。**

####3.Activity之间的通信方式
1.intent，这个大家都用过不展开。这里需要说的一点就是，intent的携带数据大小是有限制的，在505Kb,感觉应该是512，后来想想应该是intent本身也需要占一定的大小吧。
2.借助类的静态变量。（但是这个不安全）
3.借助全局变量来实现/Application，这个就是借助全局实例application。
4.借助外部存储实现通讯，
4.1使用sharedpreference实现activity之间的数据通信。 不足之处在于——从SharedPreference的API就可以看出，采用SharedPreference只能存取标准数据类型的变量值int、float、long、boolean、String。对与一些复杂类型的就捉襟见肘了。而且sp不支持多线程，而且是线程不安全的。
4.2使用sqlite来实现数据共享通信。
4.3使用file来实现（其实sp和sqlite都是基于file的）
5.借助service来实现。（也要根据使用尝尽，避免大材小用，和service挂掉）
6.通过EventBus/rxBus 进行通信
7.通过广播

####4.Activity各种情况下的生命周期
1.正常情况下是：onCreate()->onStart()->onResume()->onPause()->onStop()->onDestory()
2.Home键,back键，电源键，进程管理器：
按下home键:前台Activity依次回调onPause, onStop；
再次打开:onRestart->onstart->onResume;
按下back键:前台Activity依次回调onPause, onStop, onDestroy；
按下电源键：前台Activity依次onPause, onStop；
对于进程管理器，采用的force-stop的方式，也是直接强杀进程，并且连广播都会隔断。

####5.横竖屏切换的时候，Activity 各种情况下的生命周期
1.未做任何配置，竖屏切换横屏
```java
03-12 15:45:11.658 18429-18429/zs.com.empty E/生命周期: onPause
03-12 15:45:11.660 18429-18429/zs.com.empty E/生命周期: onsaveInstanceState
03-12 15:45:11.661 18429-18429/zs.com.empty E/生命周期: onstop
03-12 15:45:11.661 18429-18429/zs.com.empty E/生命周期: ondestroy
03-12 15:45:11.742 18429-18429/zs.com.empty E/生命周期: oncreate
03-12 15:45:11.744 18429-18429/zs.com.empty E/生命周期: onstart
03-12 15:45:11.748 18429-18429/zs.com.empty E/生命周期: onresume
```
2.未做任何配置,竖屏切换回横屏
```java
03-12 15:47:19.646 18429-18429/zs.com.empty E/生命周期: onPause
03-12 15:47:19.646 18429-18429/zs.com.empty E/生命周期: onsaveInstanceState
03-12 15:47:19.647 18429-18429/zs.com.empty E/生命周期: onstop
03-12 15:47:19.647 18429-18429/zs.com.empty E/生命周期: ondestroy
03-12 15:47:19.717 18429-18429/zs.com.empty E/生命周期: oncreate
03-12 15:47:19.718 18429-18429/zs.com.empty E/生命周期: onstart
03-12 15:47:19.723 18429-18429/zs.com.empty E/生命周期: onresume
```
3.设置android:configChanges="orientation|screenSize",横竖屏不走其他生命周期


####6.Activity与Fragment之间生命周期比较
1.上图
![activity与fragment生命周期](http://img.blog.csdn.net/20151210201347582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
1.生命周期log
在创建的过程中，是Activity带领Fragment执行生命周期中的方法，所以生命周期方法的执行顺序是这样的：

1.Activity--onCreate();

2.Fragment--onAttach();

3.Fragment--onCreate();

4.Fragment--onCreateView();

5.Fragment--onActivityCreated();

接着是这样的：

6.Activity--onStart();

7.Fragment--onStart();

8.Activity--onResume();

9.Fragment--onResume();

我们知道，无论对于Activity还是对于Fragment，onResume这个生命周期都是他们执行时间最长的，当我们的Activity或者Fragment打开之后，它就一直处于这个生命周期中。

当销毁的时候，春江水暖鸭先知，当然是Fragment先感知到，于是销毁的时候就是Fragment带领Activity：

10.Fragment--onPause();

11.Activity--onPause();

12.Fragment--onStop();

13.Activity--onStop();

14.Fragment--onDestroyView();

15.Fragment--onDestroy();

16.Fragment--onDetach();

17.Activity--onDestroy();

上面这个顺序有一个前提，就是我们所有的日志打印代码都是紧挨着super方法写。因为如果我们如果把Fragment写在了布局文件中，同时又在Activity的onCreate()方法中的setContentView之后打印日志，那么我们看到的生命周期的执行顺序就会有所不同，不过只是细微的差别，这点大家自己研究，道理也很好明白。

####7.Activity上有Dialog的时候按Home键时的生命周期
1.在activity上弹出dialog样式的activity：onpause,关闭dialog后 onresume
2.针对上面问题
```java
03-12 16:56:11.137 27495-27495/zs.com.empty E/生命周期: onPause
03-12 16:56:11.161 27495-27495/zs.com.empty E/other生命周期: oncreate
03-12 16:56:11.164 27495-27495/zs.com.empty E/other生命周期: onstart
03-12 16:56:11.168 27495-27495/zs.com.empty E/other生命周期: onresume
//按home键后
03-12 16:56:16.729 27495-27495/zs.com.empty E/other生命周期: onPause
03-12 16:56:16.737 27495-27495/zs.com.empty E/生命周期: onsaveInstanceState
03-12 16:56:16.738 27495-27495/zs.com.empty E/生命周期: onstop
03-12 16:56:16.739 27495-27495/zs.com.empty E/other生命周期: onsaveInstanceState
03-12 16:56:16.740 27495-27495/zs.com.empty E/other生命周期: onstop
//重新打开
03-12 17:03:39.855 27495-27495/zs.com.empty E/生命周期: onrestart
03-12 17:03:39.856 27495-27495/zs.com.empty E/生命周期: onstart
03-12 17:03:39.856 27495-27495/zs.com.empty E/other生命周期: onrestart
03-12 17:03:39.856 27495-27495/zs.com.empty E/other生命周期: onstart
03-12 17:03:39.857 27495-27495/zs.com.empty E/other生命周期: onresume

```
otheractivity是dialog样式的activity.

####8.两个Activity 之间跳转时必然会执行的是哪几个方法？
直接看Log,其实我觉得这种没必要问，是个程序员打个log看一下就知道了。。。


```java
03-12 17:08:45.739 10183-10183/zs.com.empty E/生命周期: onPause
03-12 17:08:45.801 10183-10183/zs.com.empty E/other生命周期: oncreate
03-12 17:08:45.807 10183-10183/zs.com.empty E/other生命周期: onstart
03-12 17:08:45.811 10183-10183/zs.com.empty E/other生命周期: onresume
03-12 17:08:46.245 10183-10183/zs.com.empty E/生命周期: onsaveInstanceState
03-12 17:08:46.246 10183-10183/zs.com.empty E/生命周期: onstop

//返回
03-12 17:09:15.191 10183-10183/zs.com.empty E/other生命周期: onPause
03-12 17:09:15.200 10183-10183/zs.com.empty E/生命周期: onrestart
03-12 17:09:15.200 10183-10183/zs.com.empty E/生命周期: onstart
03-12 17:09:15.201 10183-10183/zs.com.empty E/生命周期: onresume
03-12 17:09:15.503 10183-10183/zs.com.empty E/other生命周期: onstop
03-12 17:09:15.503 10183-10183/zs.com.empty E/other生命周期: ondestroy

```

####9.前台切换到后台，然后再回到前台，Activity生命周期回调方法。
1.无聊的问题
```java
03-12 17:11:27.016 10183-10183/zs.com.empty E/生命周期: onPause
03-12 17:11:27.026 10183-10183/zs.com.empty E/生命周期: onsaveInstanceState
03-12 17:11:27.027 10183-10183/zs.com.empty E/生命周期: onstop
//后台回前台
03-12 17:11:52.256 10183-10183/zs.com.empty E/生命周期: onrestart
03-12 17:11:52.257 10183-10183/zs.com.empty E/生命周期: onstart
03-12 17:11:52.257 10183-10183/zs.com.empty E/生命周期: onresume

```

####10.Activity的四种启动模式对比
1.任务栈：任务栈Task，是一种用来放置Activity实例的容器，他是以栈的形式进行盛放，也就是所谓的先进后出，主要有2个基本操作：压栈和出栈，其所存放的Activity是不支持重新排序的，只能根据压栈和出栈操作更改Activity的顺序。启动一个Application的时候，系统会为它默认创建一个对应的Task，用来放置根Activity。默认启动Activity会放在同一个Task中，新启动的Activity会被压入启动它的那个Activity的栈中，并且显示它。当用户按下回退键时，这个Activity就会被弹出栈，按下Home键回到桌面，再启动另一个应用，这时候之前那个Task就被移到后台，成为后台任务栈，而刚启动的那个Task就被调到前台，成为前台任务栈，Android系统显示的就是前台任务栈中的Top实例Activity。
2.怎么设置呢？通过androidmanifest的android:launchMode或者通过intent的flag来设置的。
3.standard的默认模式。在这个模式下，都会默认创建一个新的实例。因此，在这种模式下，可以有多个相同的实例，也允许多个相同Activity叠加。应用场景：绝大多数Activity。
4.singleTop：栈顶复用模式，如果要开启的activity在任务栈的顶部已经存在，就不会创建新的实例，而是调用 onNewIntent() 方法。避免栈顶的activity被重复的创建。应用场景：在通知栏点击收到的通知，然后需要启动一个Activity，这个Activity就可以用singleTop，否则每次点击都会新建一个Activity。
5.singleTask：栈内复用模式， activity只会在任务栈里面存在一个实例。如果要激活的activity，在任务栈里面已经存在，就不会创建新的activity，而是复用这个已经存在的activity，调用 onNewIntent() 方法，并且清空这个activity任务栈上面所有的activity。应用场景：大多数App的主页。对于大部分应用，当我们在主界面点击回退按钮的时候都是退出应用，那么当我们第一次进入主界面之后，主界面位于栈底，以后不管我们打开了多少个Activity，只要我们再次回到主界面，都应该使用将主界面Activity上所有的Activity移除的方式来让主界面Activity处于栈顶，而不是往栈顶新加一个主界面Activity的实例，通过这种方式能够保证退出应用时所有的Activity都能报销毁。
6.singleInstance：单一实例模式，整个手机操作系统里面只有一个实例存在。不同的应用去打开这个activity 共享公用的同一个activity。他会运行在自己单独，独立的任务栈里面，并且任务栈里面只有他一个实例存在。应用场景：呼叫来电界面。这种模式的使用情况比较罕见，在Launcher中可能使用。或者你确定你需要使Activity只有一个实例。
7.LaunchMode与StartActivityForResult：我们在开发过程中经常会用到StartActivityForResult方法启动一个Activity，然后在onActivityResult()方法中可以接收到上个页面的回传值，但你有可能遇到过拿不到返回值的情况，那有可能是因为Activity的LaunchMode设置为了singleTask。5.0之后，android的LaunchMode与StartActivityForResult的关系发生了一些改变。两个Activity，A和B，现在由A页面跳转到B页面，看一下LaunchMode与StartActivityForResult之间的关系：
![android5.0之前](https://upload-images.jianshu.io/upload_images/1187237-144638fbf8298061.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
![android5.0之后](https://upload-images.jianshu.io/upload_images/1187237-864d6df150cf2142.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
这是为什么呢？
这是因为ActivityStackSupervisor类中的startActivityUncheckedLocked方法在5.0中进行了修改。在5.0之前，当启动一个Activity时，系统将首先检查Activity的launchMode，如果为A页面设置为SingleInstance或者B页面设置为singleTask或者singleInstance,则会在LaunchFlags中加入FLAG_ACTIVITY_NEW_TASK标志，而如果含有FLAG_ACTIVITY_NEW_TASK标志的话，onActivityResult将会立即接收到一个cancle的信息，而5.0之后这个方法做了修改，修改之后即便启动的页面设置launchMode为singleTask或singleInstance，onActivityResult依旧可以正常工作，也就是说无论设置哪种启动方式，StartActivityForResult和onActivityResult()这一组合都是有效的。所以如果你目前正好基于5.0做相关开发，不要忘了向下兼容，这里有个坑请注意避让。

####11.Activity状态保存于恢复
1.onSaveInstanceState(),onSaveInstanceState() 方法用来在Activity被强制销毁之前保存数据，onSaveInstanceState()方法会携带一个 Bundle类型的参数，Bundle提供了一系列的方法用于保存数据，比如可以使用 putString()方法保存字符串，使用 putInt()方法保存整型数据。每个保存方法需要传入两个参数，第一个参数是键，第二个参数是真正要保存的内容,这个方法是异常销毁activity时才会调用，正常销毁不会调用，而且需要些super方法，这样可以让系统保存基本控件的数据。
2.onRestoreInstanceState(),只有在Bundle不为空时才会回调，而在onCreate方法当中是通过判空操作来判断是否有需要恢复的状态的。（所以使用onRestoreInstanceState,或者是onCreate依据项目需求来定。）

####12.fragment各种情况下的生命周期
1.这个篇幅有点长，暂时不在这里展开。

####13.Fragment状态保存startActivityForResult是哪个类的方法，在什么情况下使用？
1.Activity、FragmentActivity、Fragment中都有startActivityForResult()方法，也都有用以接收结果的onActivityResult()方法。
2.假设有一个FragmentActivity中嵌套一个Fragment，它们各自使用startActivityForResult发起数据请求。 经测，目标所返回结果数据，能否被它们各自的onActivityResult方法所接收的情况如下：
![结果](http://img.blog.csdn.net/20161119124707067)

- Fragment和FragmentActivity都能接收到自己的发起的请求所返回的结果
- FragmentActivity发起的请求，Fragment完全接收不到结果
- Fragment发起的请求，虽然在FragmentActivity中能获取到结果，但是requestCode完全对应不上  

3.设计目的就是为了把来自Fragment的请求和来自FragmentActivity自身请求区分开来。
4.状态保存？这个有待验证，但是有个说法：
**当Activity的onSaveInstanceState被调用的时候，Activity将会从View 层次(View Hierachy)中的每一个View中自动搜集View的状态。请注意，只会搜集实现了View状态保存/恢复的内部方法的View的数据。一旦onRestoreInstanceState被调用,Activity将会将这些搜集到的数据一对一的返还给View 层次里在搜集的时候提供了同样的android:id属性的View。
这就是为什么尽管Activity已经被销毁，而我们并没有做一些特别的事情来保存状态，但是EditText中键入的文本仍然能够呈现的原因。这并不是什么魔法，这些View 的状态已经被自动的保存和恢复回来了。
这也是为什么View 在没有被设置android:id属性的时候不能保存和恢复自己的状态的原因。
尽管这些View 的状态被自动的保存了，但是Activity的成员变量并不会有同样的效果。这些成员变量会被和Activity一起销毁。你可以手动的保存和恢复它们，通过onSaveInstanceState和onRestoreInstanceState方法。
需要验证一下。
**
如果Fragment被系统销毁，所有事情都会发生的像Activity发生的那样。
这意味着每一个单独的成员变量被销毁了。你必须分别地通过onSaveInstanceState和onActivityCreated方法，手动的保存和恢复这些变量。 请注意在Fragment里面没有onRestoreInstanceState方法存在。

**Fragment状态保存/恢复最佳实践的第一条件就是...
应用中使用的每一个单独的View都必须在内部实现状态的保存和恢复
Android内部通过onSaveInstanceState 和 onRestoreInstanceState 方法提供了保存和恢复View 状态的机制。开发者的任务就是实现它。**
![代码](http://img.blog.csdn.net/20160919173812553?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
**为了使你的代码变得干净和可扩展，你最好把Fragment状态和View状态分开处理。如果这里有任何属性是属于View的，在View内部进行保存和恢复.如果这里有任何属性是属于Fragment的，在Fragment内部进行保存和恢复。这里有一个例子。**

**总结：以上说的就是，如果是fragment的状态那就用onSaveInstanceState和onActivityCreated方法这两个方法，如果是view的状态保存就用extends view的方法onSaveInstanceState和onRestoreInstanceState方法，所以分开处理，如果第三方组件也要继承一下然后重写这两个方法，记得在布局上写id.**

贴个地址[blog](http://blog.csdn.net/u011694328/article/details/52587652)

####14.如何实现Fragment的滑动？
1.手势识别，GestureDetector
2.viewpager

####15.fragment之间传递数据的方式？
1.通过activity和tag获取需要设置的fragment
```java

//MainFragment.java文件中
public void setData(String string) {
    bt_main.setText(string);
}

lv.setOnItemClickListener(new AdapterView.OnItemClickListener() {
    @Override
     public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
          MainFragment mainFragment =
               (MainFragment) getActivity()
               .getSupportFragmentManager()
               .findFragmentByTag("mainFragment");
          mainFragment.setData(mDatas.get(position));
     }
});


```
2.采取接口回调的方式进行数据传递。
```java
//MenuFragment.java文件中
public interface OnDataTransmissionListener {
    public void dataTransmission(String data);
}
public void setOnDataTransmissionListener(OnDataTransmissionListener mListener) {
    this.mListener = mListener;
}

//MenuFragment.java文件中
lv.setOnItemClickListener(new AdapterView.OnItemClickListener() { 
   @Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
        /**
         * 方法二：采取接口回调的方式进行数据传递
         */
        if (mListener != null) {
            mListener.dataTransmission(mDatas.get(position));
        }
    }
});

//在MainActivity.java中
menuFragment.setOnDataTransmissionListener(new MenuFragment.OnDataTransmissionListener() {
    @Override
    public void dataTransmission(String data) {
        mainFragment.setData(data);  //注：对应的mainFragment此时应该要用final进行修饰
    }
});




```
3.EventBus,不展开

####16.Activity 怎么和Service 绑定？
1.简单说就是在activity里重写ServiceConnection，然后通过intent来bindservice就行了，
2.连接上之后就会在ServiceConnection里有个回调onServiceConnected。在回调里写业务。
3.上代码看看
```java
  private Myserver.Mybinder mybinder;
    private  boolean bindserver;
    private Button bindbtn;

    private ServiceConnection serviceConnection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            mybinder = (Myserver.Mybinder) service;
            Log.e("OtherActivity","service connectioned");
            mybinder.playMusic();
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_other);
        Log.e("other生命周期","oncreate");
        bindbtn = (Button) findViewById(R.id.bindbtn);
        bindbtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(OtherActivity.this,Myserver.class);
//                OtherActivity.this.startService(intent);
                bindserver = OtherActivity.this.bindService(intent,serviceConnection,BIND_AUTO_CREATE);
                Log.e("OtherActivity","bindbtn");

            }
        });
    }
//记得在manifest文件中注册service.

package zs.com.empty;

import android.app.Service;
import android.content.Intent;
import android.os.Binder;
import android.os.IBinder;
import android.support.annotation.Nullable;
import android.util.Log;
import android.widget.Toast;

/**
 * Created by zhaoshuang on 2018/3/15.
 */

public class Myserver extends Service{

    private Mybinder myBinder = new Mybinder();

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return myBinder;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        Log.e("Myserver","onCreate");
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.e("Myserver","onstartCommand");
        return super.onStartCommand(intent, flags, startId);
    }

    class Mybinder extends Binder{

        public void playMusic(){
            Toast.makeText(Myserver.this, "音乐在后台播放", Toast.LENGTH_SHORT).show();
        }

        public void getProgress(){
            Toast.makeText(Myserver.this, "播放到80%", Toast.LENGTH_SHORT).show();
        }
    }

}


```

####17.service和activity怎么进行数据交互？
1.broadcast
2.bindService
####18.Service的开启方式
1.通过startService创建的service创建后就跟对应的activity没有关系，当activity销毁时，Service依然在运行（前提是没有调用stopService（））
2.通过startService()方法启动的service，会调用onCreate（），onStartCommand（）方法，如果Service已经被创建，则不会再调用onCreate（）方法，但是依然会调用onStartCommand（）方法  适用场景：音乐播放器，上传下载文件一般都是在onStartCommand（）方法中开始执行的
3.通过StartService（）方法启动的service，Service类中的onBind（）方法返回null即可
4.无论通过startService（）方法开启多少次service，只要调用一次stopService（）方法，这个服务就会停止。（Service只有一个实例，这就是StartService（）方法和stopService（）方法中的intent可以不是全局变量的原因，即使是从新new出来的service指向的也是正在运行的service）
5.StartService（）方法和stopService（）方法都是异步执行的，并且是串行执行，只有当第一次执行完才会（onStartCommand（）执行完毕）执行第二次的。
6.一个activity通过bindService（）绑定服务后会调用Service的onCreate（）和onBind（）方法，onBind（）方法会返回一个IBander对象的实例给activity，ServiceConnection的onServiceConnected（）方法中，标志着ActivityA与TestService建立了绑定连接，此时当客户端任意一个ActivityB想要在次bindservice的时候，service不会再走onCreate（）和onBind（）方法，而是直接拿到IBinder的实例（这个实例是APP中所有Activity共享的），所以在ActivityB的ServiceConnection的onServiceConnected（）方法中会直接获得IBinder的实例 ,此时如果在ActivityB中解绑Service，不会触发Service的unBindService（）和onDestory（）方法，这两个方法是当Service没有任何activity与之绑定的时候才会调用，此时还有ActivityA与之绑定。
7.不能多次调用unBindService（），会抛出异常，所以在调用该方法时需要判断，当bindService的时候将flag的值改变。
8.如果service的onBind（）方法返回null，service依然会启动，但是没有和activity绑定上，但但是此时仍然要用unBindService（）方法停止服务。

####19.谈谈你对ContentProvider的理解
1.作用：为了给不同应用提供内容访问。
2.ContentProvider 封装了数据的跨进程传输，我们可以直接使用 getContentResolver() 拿到 ContentResolver 进行增删改查即可。
3.ContentProvider 以一个或多个表（与在关系型数据库中的表类似）的形式将数据呈现给外部应用。 行表示提供程序收集的某种数据类型的实例，行中的每个列表示为实例收集的每条数据。
####20.说说ContentProvider、ContentResolver、ContentObserver 之间的关系
1.TODO
3###21.请描述一下广播BroadcastReceiver的理解
1.是四大组件之一,主要用于接收app发送的广播
2.内部通信实现机制:通过android系统的Binder机制.
3.广播分为两种:
- 无序广播 
优点:完全异步,逻辑上可被任何接受者收到广播,效率高
缺点:接受者不能讲处理结果交给下一个接受者,且无法终止广播.
- 有序广播 
按被接收者的优先级循序传播
A>B>C,每个都有权终止广播,下一个就得不到
每一个都可进行修改操作,下一个就得到上一个修改后的结果.
4.最终广播者:
- Context.sendOrderedBroadcast(intent,receiverPermission,resultReceiver,scheduler,initialCode,initialData,initialExtras)时我们可以指定resultReceiver为最终广播接收者.
- 如果比他优先级高的接受者不终止广播,那么他的onReceive会执行两次 
- 第一次是正常的接收
- 第二次是最终的接收
- 如果优先级高的那个终止广播,那么还是依然收到一次最终的广播  

5.常见的广播接收者运用场景:
- 开机启动,sd卡挂载,低电量,外拨电话,锁屏等
- 比如根据产品经理要求,设计播放音乐时,锁屏是否决定暂停音乐.
[广播常见面试题](http://blog.csdn.net/nzfxx/article/details/51835743)

####21.广播使用的方式和场景
1.开机启动,sd卡挂载,低电量,外拨电话,锁屏等
2.比如根据产品经理要求,设计播放音乐时,锁屏是否决定暂停音乐.
3.内部消息传递。
4.App全局监听，这种主要用于在AndroidManifest中静态注册的广播接收器，一般我们在收到该消息后，需要做一些相应的动作，而这些动作与当前App的组件，比如Activity或者Service的是否运行无关，比如我们在集成第三方Push SDK时，一般都会添加一个静态注册的BroadcastReceiver来监听Push消息，当有Push消息过来时，会在后台做一些网络请求或者发送通知等等。
5.组件局部监听，这种主要是在Activity或者Service中使用registerReceiver()动态注册的广播接收器，因为当我们收到一些特定的消息，比如网络连接发生变化时，我们可能需要在当前Activity页面给用户一些UI上的提示，或者将Service中的网络请求任务暂停。所以这种动态注册的广播接收器适合特定组件的特定消息处理。
6.关于BroadcastReceiver使用需要注意的几点：
- onReceive中不能执行耗时操作，如果耗时超过10s会弹出ANR。
- onReceive中context参数，如果是静态注册的广播，context为ReceiverRestrictedContext，所在如果在这里要启动一个Activity的话（调用startActivity），需要在intent中添加Intent.FLAG_ACTIVITY_NEW_TASK；如果是动态注册的广播，context为当前注册时所在的组件，比如Activity或者Service。
- 监听系统广播，需要在AndroidManifest中申请权限，此外，Android高版本系统对于一些重要的系统广播，比如开机启动，网络连接，电量变化，锁屏等做了限制，如果需要监听这些广播，需要做系统兼容性处理。
- 普通广播的广播接收器是并行无序执行的，有序广播的广播接收器按照广播优先级串行执行  

####22.本地广播和全局广播有什么差别？
1.BroadcastReceiver是针对应用间、应用与系统间、应用内部进行通信的一种方式
2.LocalBroadcastReceiver仅在自己的应用内发送接收广播，也就是只有自己的应用能收到，数据更加安全广播只在这个程序里，而且效率更高。
3.BroadcastReceiver 使用
    - 制作intent（可以携带参数）
    - 使用sendBroadcast()传入intent;
    - 制作广播接收器类继承BroadcastReceiver重写onReceive方法（或者可以匿名内部类啥的）
    - 在java中（动态注册）或者直接在Manifest中注册广播接收器（静态注册）使用registerReceiver()传入接收器和intentFilter
    - 取消注册可以在OnDestroy()函数中，unregisterReceiver()传入接收器 

4.LocalBroadcastReceiver 使用.LocalBroadcastReceiver不能静态注册，只能采用动态注册的方式。
在发送和注册的时候采用，LocalBroadcastManager的sendBroadcast方法和registerReceiver方法.

####23.AlertDialog,popupWindow,Activity区别
####24.Application 和 Activity 的 Context 对象的区别
####25.Android属性动画特性
####26.如何导入外部数据库?
####27.LinearLayout、RelativeLayout、FrameLayout的特性及对比，并介绍使用场景
####28.谈谈对接口与回调的理解
####29.回调的原理
####30.介绍下SurfView
####31.序列化的作用，以及Android两种序列化的区别
####32.差值器
####33.估值器
####34.Android中数据存储方式

