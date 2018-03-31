title: Service的startService与bindService的区别
author: superman
date: 2018-02-07 22:11:45
categories:
- android
tags:
- Service
---
Android执行Service有两种方法，一种是startService，一种是bindService。下面让我们一起来聊一聊这两种执行Service方法的区别。
<!--more-->


![](http://upload-images.jianshu.io/upload_images/545982-707a3bbe52ae3c54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#####1、生命周期上的区别

执行startService时，Service会经历onCreate->onStartCommand。当执行stopService时，直接调用onDestroy方法。调用者如果没有stopService，Service会一直在后台运行，下次调用者再起来仍然可以stopService。

执行bindService时，Service会经历onCreate->onBind。这个时候调用者和Service绑定在一起。调用者调用unbindService方法或者调用者Context不存在了（如Activity被finish了），Service就会调用onUnbind->onDestroy。这里所谓的绑定在一起就是说两者共存亡了。

多次调用startService，该Service只能被创建一次，即该Service的onCreate方法只会被调用一次。但是每次调用startService，onStartCommand方法都会被调用。Service的onStart方法在API 5时被废弃，替代它的是onStartCommand方法。

第一次执行bindService时，onCreate和onBind方法会被调用，但是多次执行bindService时，onCreate和onBind方法并不会被多次调用，即并不会多次创建服务和绑定服务。

#####2、调用者如何获取绑定后的Service的方法

onBind回调方法将返回给客户端一个IBinder接口实例，IBinder允许客户端回调服务的方法，比如得到Service运行的状态或其他操作。我们需要IBinder对象返回具体的Service对象才能操作，所以说具体的Service对象必须首先实现Binder对象。

#####3、既使用startService又使用bindService的情况

如果一个Service又被启动又被绑定，则该Service会一直在后台运行。首先不管如何调用，onCreate始终只会调用一次。对应startService调用多少次，Service的onStart方法便会调用多少次。Service的终止，需要unbindService和stopService同时调用才行。不管startService与bindService的调用顺序，如果先调用unbindService，此时服务不会自动终止，再调用stopService之后，服务才会终止；如果先调用stopService，此时服务也不会终止，而再调用unbindService或者之前调用bindService的Context不存在了（如Activity被finish的时候）之后，服务才会自动停止。

那么，什么情况下既使用startService，又使用bindService呢？

如果你只是想要启动一个后台服务长期进行某项任务，那么使用startService便可以了。如果你还想要与正在运行的Service取得联系，那么有两种方法：一种是使用broadcast，另一种是使用bindService。前者的缺点是如果交流较为频繁，容易造成性能上的问题，而后者则没有这些问题。因此，这种情况就需要startService和bindService一起使用了。

另外，如果你的服务只是公开一个远程接口，供连接上的客户端（Android的Service是C/S架构）远程调用执行方法，这个时候你可以不让服务一开始就运行，而只是bindService，这样在第一次bindService的时候才会创建服务的实例运行它，这会节约很多系统资源，特别是如果你的服务是远程服务，那么效果会越明显（当然在Servcie创建的时候会花去一定时间，这点需要注意）。    

#####4、本地服务与远程服务

本地服务依附在主进程上，在一定程度上节约了资源。本地服务因为是在同一进程，因此不需要IPC，也不需要AIDL。相应bindService会方便很多。缺点是主进程被kill后，服务变会终止。

远程服务是独立的进程，对应进程名格式为所在包名加上你指定的android:process字符串。由于是独立的进程，因此在Activity所在进程被kill的是偶，该服务依然在运行。缺点是该服务是独立的进程，会占用一定资源，并且使用AIDL进行IPC稍微麻烦一点。

对于startService来说，不管是本地服务还是远程服务，我们需要做的工作都一样简单。

5、代码实例

startService启动服务
```
public class LocalService1 extends Service {
    /**
    * onBind 是 Service 的虚方法，因此我们不得不实现它。
    * 返回 null，表示客服端不能建立到此服务的连接。
    */
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
    
    @Override
    public void onCreate() {
        super.onCreate();
    }
    
    @Override
    public void onStartCommand(Intent intent, int startId, int flags) {
        super.onStartCommand(intent, startId, flags);
    }
    
    @Override
    public void onDestroy() {
        super.onDestroy();
    }
}
```
bindService绑定服务
```
public class LocalService extends Service {

    public SimpleBinder sBinder;

    /**
    * 在 Local Service 中我们直接继承 Binder 而不是 IBinder,因
    为 Binder 实现了 IBinder 接口，这样我们可以** 少做很多工
    作。
    */
    public class SimpleBinder extends Binder{
        /**
        * 获取 Service 实例
        * @return
        */
        public LocalService getService(){
            return LocalService.this;
        }
    }

    public int add(int a, int b){
        return a + b;
    }


    @Override
    public void onCreate() {
        super.onCreate();
        // 创建 SimpleBinder
        sBinder = new SimpleBinder();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // 返回 SimpleBinder 对象
        return sBinder;
    }
}
```
上面的代码关键之处，在于 onBind(Intent) 这个方法 返回了一个实现了 IBinder 接口的对象，这个对象将用于绑定Service 的 Activity 与 Local Service 通信。

下面是 Activity 中的代码：
```
public class Main extends Activity {
    private final static String TAG = "SERVICE_TEST";
    private ServiceConnection sc;
    private boolean isBind;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        sc = new ServiceConnection() {
            @Override
            public void onServiceDisconnected(ComponentName name) {
            }
    
            @Override
            public void onServiceConnected(ComponentName name, IBinder service) {
                LocalService.SimpleBinder sBinder = (LocalService.SimpleBinder)service;
                Log.v(TAG, "3 + 5 = " + sBinder.add(3, 5));
                Log.v(TAG, sBinder.getService().toString());
            }
        };

        findViewById(R.id.btnBind).setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                bindService(new Intent(Main.this, LocalService.class), sc, Context.BIND_AUTO_CREATE);
                isBind = true;
            }
        });

        findViewById(R.id.btnUnbind).setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                if(isBind){
                    unbindService(sc);
                    isBind = false;
                }
            }
        });
    }
}
```
#####6、在AndroidManifest.xml里Service元素常见选项  
|属性|作用|
|--------|--------|
|android:name|服务类名|
|android:label|服务的名字，如果此项不设置，那么默认显示的服务名则为类名|
|android:icon|服务的图标|
|android:permission|申明此服务的权限，这意味着只有提供了该权限的应用才能控制或连接此服务|
|android:process|表示该服务是否运行在另外一个进程，如果设置了此项，那么将会在包名后面加上这段字符串表示另一进程的名字|
|android:enabled|表示是否能被系统实例化，为true表示可以，为false表示不可以，默认为true|
|android:exported|表示该服务是否能够被其他应用程序所控制或连接，不设置默认此项为 false|

转载:https://my.oschina.net/tingzi/blog/376545