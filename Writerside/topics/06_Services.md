# Services

A service **runs in the main thread of its hosting process**; the service does not create its own thread and does not run in a separate process unless you specify otherwise.

You should **run any blocking operations on a separate thread** within the service to avoid Application Not Responding (ANR) errors.

> If you do use a service, it still runs in your application's main thread by default, so you should still create a new thread within the service if it performs intensive or blocking operations.

## Types of Services

### Foreground Service:

A foreground service performs some operation that is **noticeable to the user**.

Foreground services must **display a Notification**.

Foreground services continue running **even when the user isn't interacting with the app**.(It stays alive even when the app is closed.)

> The WorkManager API offers a flexible way of scheduling tasks, and is able to run these jobs as foreground services if needed.

For example, Media Playback, Uploading a file to a server, File download, etc.

### Background Service:

A background service performs an operation that **isn't directly noticed by the user**.

It runs only when the app is running. (It is terminated when the app is terminated.)

> For API level 26 or higher, the system imposes restrictions on running background services when the app itself isn't in the foreground. Instead, schedule tasks using WorkManager.

For example, Data Syncing, Data Backup, Peridic Cleanup, etc.

### Bound Service:

A bound service offers a **client-server interface** that allows components to interact with the service, send requests, receive results, and even do so across processes with interprocess communication (IPC).

---

> Service can work both ways — it can be started (to run indefinitely) and also allow binding.
>
> It's simply a matter of whether you implement a couple of callback methods: onStartCommand() to allow components to start it and onBind() to allow binding.

The Android system **stops a service** only when **memory is low** and it must **recover system resources for the activity that has user focus**.

To ensure that your app is secure, always **use an explicit intent when starting a Service** and **don't declare intent filters** for your services.

## Intent Service

A Service runs on the main thread of the application by default. So if your application performs intensive work while the user interacts with it, the **service will slow down the user experience**.

The Android framework also provides the IntentService subclass of Service that **uses a worker thread** to handle all of the start requests, one at a time.

To avoid impacting the user experience, you can start a Service in the background using an IntentService. The IntentService class provides a straightforward structure for running an operation on a single background thread.

This allows it to **handle long-running operations without affecting your user interface**'s responsiveness.

IntentService **runs on a single background thread**, so if you have multiple requests, they will be queued and processed one after the other, it could also stop itself after processing all intents.

> Using this class is not recommended for new apps as it will not work well starting with Android 8 Oreo, due to the introduction of Background execution limits.

**MainActivity.java**

```Java
Intent intent = new Intent(MainActivity.this, MyIntentService.class);
startService(intent);
```

**MyIntentService.java**

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
