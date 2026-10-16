# Services
<show-structure depth="2"/>

A Service is an application component that can perform long-running operations in the background. 
It does not provide a user interface. 

Once started, a service might continue running for some time, even after the user switches to another application. 
Additionally, a component can bind to a service to interact with it and even perform interprocess communication (IPC).

For example, a service can handle network transactions, play music, perform file I/O, 
or interact with a content provider, all from the background.

> A service runs in the main thread of its hosting process; 
> the service does not create its own thread and does not run in a separate process unless you specify otherwise. 
> 
> You should run any blocking operations on a separate thread within the service 
> to avoid Application Not Responding (ANR) errors.
{style="warning"}

## Types of Services

### Foreground

A foreground service performs some operation that is noticeable to the user. 

For example, an audio app would use a foreground service to play an audio track. 

Foreground services must display a Notification. 

Foreground services continue running even when the user isn't interacting with the app.

When you use a foreground service, you must display a notification 
so that users are actively aware that the service is running. 
This notification cannot be dismissed unless the service is either stopped or removed from the foreground.

### Background

A background service performs an operation that isn't directly noticed by the user. 

For example, if an app used a service to compact its storage, that would usually be a background service.

> Note: If your app targets API level 26 (Android 8.0) or higher, 
> the system imposes restrictions on running background services when the app itself isn't in the foreground.
> 
> In most situations, for example, you shouldn't access location information from the background. 
> Instead, schedule tasks using WorkManager.
{style="note"}

### Bound

A service is bound when an application component binds to it by calling `bindService(`). 

A bound service offers a client-server interface that allows components to 
interact with the service, send requests, receive results, 
and even do so across processes with interprocess communication (IPC). 

A bound service runs only as long as another application component is bound to it. 

Multiple components can bind to the service at once, but when all of them unbind, the service is destroyed.

### Types of Services: Summary

Although this documentation generally discusses started and bound services separately, 
your service can work both ways—it can be **started** (to run indefinitely) and also allow **binding**. 

It's simply a matter of whether you implement a couple of callback methods: 
`onStartCommand()` to allow components to start it and `onBind()` to allow binding.

Regardless of whether your service is started, bound, or both, 
any application component can use the service (even from a separate application) 
in the same way that any component can use an activity—by starting it with an `Intent`. 

However, you can declare the service as _private_ in the manifest file and block access from other applications.

## Choosing between a service and a thread

A service is simply a component that can run in the background, 
even when the user is not interacting with your application, 
so you should create a service only if that is what you need.

If you must perform work outside of your main thread, 
but only **while the user is interacting with your application**, 
you should instead create a new thread in the context of another application component.

For example, if you want to play some music, but only while your activity is running, 
you might create a thread in `onCreate()`, start running it in `onStart()`, and stop it in `onStop()`. 

Also consider using thread pools and executors from the `java.util.concurrent` package 
or Kotlin coroutines instead of the traditional `Thread` class.

## Service Lifecycle

![service_lifecycle.png](service_lifecycle.png)

To create a service, you must create a subclass of Service or use one of its existing subclasses. 

In your implementation, you must override some callback methods that handle key aspects of the service lifecycle 
and provide a mechanism that allows the components to bind to the service, if appropriate.

### `onStartCommand()`

The system invokes this method by calling `startService()` when another component (such as an activity) 
requests that the service be started. 

When this method executes, the service is started and can run in the background indefinitely. 

If you implement this, it is your responsibility to stop the service when its work is complete 
by calling `stopSelf()` or `stopService()`. 

If you only want to provide binding, you don't need to implement this method.

### `onBind()`

The system invokes this method by calling `bindService()` when another component wants to bind 
with the service (such as to perform RPC). 

In your implementation of this method, you must provide an interface 
that clients use to communicate with the service by returning an `IBinder`. 

You must always implement this method; however, if you don't want to allow binding, you should return null.

### `onCreate()`

The system invokes this method to perform one-time setup procedures 
when the service is initially created (before it calls either `onStartCommand()` or `onBind()`). 

If the service is already running, this method is not called.

### `onDestroy()`

The system invokes this method when the service is no longer used and is being destroyed. 

Your service should implement this to clean up any resources such as threads, registered listeners, or receivers. 

This is the last call that the service receives.

### Service Lifecycle:  Summary

If a component starts the service by calling `startService()` (which results in a call to `onStartCommand()`), 
the service continues to run until it stops itself with `stopSelf()` or 
another component stops it by calling `stopService()`.

If a component calls `bindService()` to create the service and `onStartCommand()` is not called, 
the service runs only as long as the component is bound to it. 
After the service is unbound from all of its clients, the system destroys it.

## Intent Service

- Automatically runs on a **single background thread**
- Processes intents sequentially (queue, since it's a single thread)
- Stops itself when done

Why deprecated:
- Poor flexibility
- Replaced by better tools

> Using this class is not recommended for new apps 
> as it will not work well starting with Android 8 Oreo, 
> due to the introduction of Background execution limits.

`MainActivity.java`

```Java
Intent intent = new Intent(MainActivity.this, MyIntentService.class);
startService(intent);
```

`MyIntentService.java`

```Java
public class MyIntentService extends IntentService {
    private static final String TAG = "MyIntentService";
    public static final String name = "IntentWorker";
    public MyIntentService() {
        super(name);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        Log.d(TAG, "For intent : " + intent.getAction());

        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        Log.d(TAG, "Task Completed");
    }
}
```

> Specify the Service in Manifest file as well.
