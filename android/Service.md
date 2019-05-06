###  Android四大组件之Service
#### 本地服务(LocalService)
>调用者和service在同一个进程里，所以运行在主进程的main线程中。所以不能进行耗时操作，可以采用在service里面创建一个Thread来执行任务。service影响的是进程的生命周期，讨论与Thread的区别没有意义。
任何 Activity 都可以控制同一 Service，而系统也只会创建一个对应 Service 的实例。

**第一种启动方式**
通过start方式开启服务.

**使用Service的步骤**

>1. 定义一个类继承Service
>2. 在Manifest文件中注册Service
>3. 使用Context的startService(Intent)启动Service
>4. 不再使用时，调用Context的stopService(Intent)停止服务  

**使用start方式启动的生命周期**
> 依次调用onCreate() -> onStartCommand() -> onDestroy() 
> **注意：**如果服务已经开启，不会重复回调onCreate()，如果再次调用Context.startService()，Service会调用onStartCommand()，服务停止时回调onDestroy()被销毁

**特点**

>一旦服务开启就跟调用者没有任何关系，调用者退出或者挂了，服务还在后台长期运行，调用者不能调用服务里的方法

**第二种启动方式**
通过bind方式启动服务.
>1. 定义一个类继承Service
>2. 在Manifest文件中注册Service 
>3. 使用Context的bindService(Intent,ServiceConnection,int)启动Service
>4. 不再使用时，调用Context的unbindService(ServiceConnection)停止服务

**使用bind方式启动的生命周期**
>依次调用onCreate() -> onBind() -> onUnbind() -> onDestroy()
>**注意：**通过bind方式启动服务不会调用onStart() 或 onStartCommand()

**特点**
>bind方式启动服务，调用者挂了，服务也会跟着挂掉。调用者可以调用服务里的方法

##### 示例
**定义一个类继承Service**
```kotlin
/**
 * <p>作者：AiCoder  2019/5/1 20:15
 * <p>邮箱：codinghuang@163.com
 * <p>作用：
 * <p>描述：BindService
 */
class BindService : Service() {
    private val mBinder by lazy {
        MyBinder()
    }

    override fun onCreate() {
        super.onCreate()
        logd("onCreate")
    }

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        logd("onStartCommand")
        return super.onStartCommand(intent, flags, startId)
    }

    override fun onDestroy() {
        super.onDestroy()
        logd("onDestroy")
    }

    override fun onBind(intent: Intent?): IBinder? {
        logd("onBind")
        return mBinder
    }

    override fun onUnbind(intent: Intent?): Boolean {
        logd("onUnbind")
        return super.onUnbind(intent)
    }

    /*** 用于Service和Activity进行通信*/
    class MyBinder : Binder() {

        fun sayHello(str: String) = logd("information from activity is:  $str")
        
        fun download(str: String) = logd("开始下载链接： $str")
    }
}
```

**在AndroidManifest的application结点中注册Service**
```kotlin
 <service
 	android:name=".service.BindService"
	android:exported="true"/>
```

**通过startService方式启动Service**

```kotlin
private val startIntent by lazy {
	Intent(this, BindService::class.java)
}

btnStart.setOnClickListener {
	startService(startIntent)
}
```

**通过bindService方式启动Service**
```kotlin
private val bindIntent by lazy {
	Intent(this, BindService::class.java)
}

//通过Binder调用BindService中定义的方法
private var mBinder: BindService.MyBinder? = null
//通过ServiceConnection获取MyBinder对象
private val mCnn by lazy {
	object : ServiceConnection {
		override fun onServiceDisconnected(name: ComponentName?) = logd("断开服务")

		override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
			logd("服务已连接")
			mBinder = service as BindService.MyBinder
            
            mBinder?.sayHello("你好，我在Activity中")
			mBinder?.download("http://www.baidu.com")
		}
	}
}

//绑定服务
btnBind.setOnClickListener {
	bindService(bindIntent, mCnn, Context.BIND_AUTO_CREATE)
}
```

#### 远程服务(RemoteService)
>调用者和service不在同一个进程中，service在单独的进程中的main线程，是一种垮进程通信方式。

**绑定远程服务的步骤**
