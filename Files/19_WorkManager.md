# WorkManager

WorkManager is the recommended solution for **persistent work**.

> **Work is persistent** when it remains scheduled through app restarts and system reboots.

Because most background processing is best accomplished through persistent work, WorkManager is the primary **recommended API for background processing**.

> Android WorkManager is a part of Android Jetpack (a suite of libraries to guide developers to write quality apps) and is one of the Android Architecture Components (collection of components that help developers design robust, testable, and easily maintainable apps).

For example, Sync data from Server

> Unlike WorkManager, AlarmManager wakes a device from Doze mode.
>
> It is therefore not efficient in terms of power and resource management. Only use it for precise alarms or notifications such as calendar events — not background work.

## Types of persistent work

WorkManager handles three types of persistent work:

- Immediate: Tasks that must begin immediately and complete soon. May be expedited.

- Long Running: Tasks which might run for longer, potentially longer than 10 minutes.

- Deferrable: Scheduled tasks that start at a later time and can run periodically.

![Work Manager](../Images/workmanager_main.svg)

## Use WorkManager for reliable work

WorkManager is intended for work that is required to run reliably even if the user navigates off a screen, the app exits, or the device restarts. For example:

- Sending logs or analytics to backend services.
- Periodically syncing application data with a server.

WorkManager is not intended for in-process background work that can safely be terminated if the app process goes away.

It is also not a general solution for all work that requires immediate execution.

## Implementation

**Define MyWorker class** that **extends Worker class** and **override doWork()** method.

```java
public class MyWorker extends Worker {
    private static final String TAG = "MyWorker";
    public MyWorker(@NonNull Context context, @NonNull WorkerParameters workerParams) {
        super(context, workerParams);
    }

    // This methods runs on background thread by default
    @NonNull
    @Override
    public Result doWork() {
        Data data = getInputData();
        int number = data.getInt("number", -1);
        Log.d(TAG, "doWork: number = " + number);

        for (int i = 0; i < number; i++) {
            Log.d(TAG, "doWork: i = " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
                return Result.failure();
            }
        }
        return Result.success();
    }
}
```

**In MainActivity.java, Initial setup :**

```java
Data data = new Data.Builder()
        .putInt("number", 10)
        .build();

Constraints constraints = new Constraints.Builder()
        .setRequiresStorageNotLow(true)
        .setRequiredNetworkType(NetworkType.CONNECTED)
        .build();

```

**Enqueue the one time work request :**

```java

OneTimeWorkRequest oneTimeWorkRequest = new OneTimeWorkRequest.Builder(MyWorker.class)
        .setInputData(data)
        .setConstraints(constraints)
        .setInitialDelay(5, TimeUnit.HOURS)
        .addTag("download")
        .build();

WorkManager.getInstance(this).enqueue(oneTimeWorkRequest);
```

**Enqueue the periodic work request :**

```java
PeriodicWorkRequest periodicWorkRequest = new PeriodicWorkRequest.Builder(MyWorker.class, 3, TimeUnit.DAYS)
        .setInputData(data)
        .setConstraints(constraints)
        .setInitialDelay(5, TimeUnit.HOURS)
        .addTag("reminder")
        .build();

WorkManager.getInstance(this).enqueue(periodicWorkRequest);
```

**Cancel the work request :**

```java
WorkManager.getInstance(this).cancelWorkById(periodicWorkRequest.getId());
```

**Chaining Multiple Work :**

```java
WorkManager.getInstance(this).beginWith(oneTimeWorkRequest)
        .then(oneTimeWorkRequest2)
        .enqueue();
```
