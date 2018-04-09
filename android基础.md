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
**剩下的,Broadcast和content provider在后面说吧。**

####3.Activity之间的通信方式.
