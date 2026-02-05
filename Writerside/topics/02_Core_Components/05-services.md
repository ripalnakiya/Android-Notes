# Services

A Service is an Android component that performs **long-running operations** in the **background** 
or exposes functionality to other components, without a user interface.

- Use a Service when:
  - Work must continue even if the app is not visible
  - Multiple components need to access the same background logic
  - The operation doesn’t belong to UI lifecycle

> A Service runs on the **main thread**,
> so we should create a new thread within the service,
> if it performs intensive or blocking operations to avoid ANR.

## Service Lifecycle

- **`onCreate()`**
  - Called once
  - Initialize resources
  - Equivalent to setup phase

- **`onStartCommand(Intent, flags, startId)`**
  - Called when `startService()` is invoked
  - This is where work usually begins
  - Can be called multiple times

- **`onBind(Intent)`**
  - Used for bound services
  - Returns an `IBinder`
  - If unused, return `null`

- **`onDestroy()`**
  - Cleanup

## Types of Services

### 1. Started Service

Started using:

```Java
startService(intent);
```

- Characteristics:
  - Runs indefinitely until stopped
  - Independent of the component that started it
  - Must stop itself or be stopped explicitly

Stopping:

```Java
stopSelf();
stopService(intent);
```

- Typical use:
  - File download
  - Syncing data
  - Logging
  - Uploading analytics

> Once started, a service can **run in the background indefinitely**, 
> even if the component that started it is destroyed.

### 2. Bound Service

Started using:

```Java
bindService(intent, connection, BIND_AUTO_CREATE);
```

- Characteristics:
  - Client-server model
  - Exists only while at least one client is bound
  - Exposes methods via `IBinder`

- Typical use:
  - Music playback control
  - Shared computation
  - Data providers within app

Multiple components can **bind to the service at once**, 
but when all of them unbind, the service is destroyed.

### 3. Started + Bound Service

- Started → controls lifetime
- Bound → controls communication
- Service dies only when:
  - `stopSelf()` is called and
  - all clients are unbound

## Execution Modes of Services

### Foreground Service:

A foreground service performs some operation that is **noticeable to the user**.

Foreground services must **display a Notification**.

Foreground services continue running
**even when the user isn't interacting with the app**.
(It stays alive even when the app is closed).

> Foreground service is NOT a different service...
> It is a started service with a foreground priority

- Example:
  - Music
  - Navigation
  - Ongoing call
  - Fitness tracking

> The WorkManager API offers a flexible way of scheduling tasks, 
> and is able to run these jobs as foreground services if needed.

### Background Service:

A service running without foreground notification.

A background service performs 
an operation that **isn't directly noticed by the user**.

It runs only when the app is running,
(It is terminated when the app is terminated).

> For API level 26 (Android 8.0) or higher, 
> the system imposes restrictions on running background services 
> when the app itself isn't in the foreground. 
> Instead, schedule tasks using WorkManager.

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
