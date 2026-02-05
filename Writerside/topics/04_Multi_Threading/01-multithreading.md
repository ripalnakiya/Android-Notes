# Multithreading

- Android has one sacred UI thread (aka main thread).
  - Draw UI
  - Handle touch input
  - Run lifecycle callbacks

Block it and the system crashes your app with an ANR.

- So the rule is simple:
  - Heavy work off the main thread
  - UI updates only on the main thread

## Threads in Android

```Java
new Thread(() -> {
    // background work
}).start();
```

- This works, but:
  - No lifecycle awareness
  - No easy way back to the main thread
  - Easy to leak activities

### Example

**Starting a thread**

```Java
ExampleRunnable exampleRunnable = new ExampleRunnable();
new Thread(exampleRunnable).start();
```

**Runnable**

```Java
public class ExampleRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            Log.d(TAG, "startThread: " + i);
            if(flag)
                return;
            if(i == 5) {
                // When i=5, we'll update the UI
                // We need to associate this handler with main thread
                Handler handler = new Handler(Looper.getMainLooper());

                handler.post(new Runnable() {
                    @Override
                    public void run() {
                        textView.setText(R.string.fifty_percent);
                    }
                });
                // This works for fine for Activity
                   runOnUiThread(new Runnable() {
                       @Override
                       public void run() {
                           textView.setText(R.string.fifty_percent);
                       }
                   });
            }

            // Sleep for 1 second
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## Looper

A Looper turns a thread into something useful.

- Without a Looper:
  - Thread runs
  - Executes code
  - Dies

- With a Looper:
  - Thread stays alive
  - Processes a message queue
  - Executes tasks one by one

> A Looper lets a thread wait for work instead of exiting.

The main thread already has one:
```Java
Looper.getMainLooper();
```

```Java
Handler handler = new Handler(Looper.getMainLooper());

handler.post(() -> {
    textView.setText("UI updated safely");
});
```

Background threads do not have, you can add one explicitly.

```Java
class WorkerThread extends Thread {
    Handler handler;

    public void run() {
        Looper.prepare();   // create Looper + MessageQueue
        handler = new Handler(Looper.myLooper());
        Looper.loop();      // start processing messages
    }
}
```

## MessageQueue: The Inbox

Each Looper owns a **MessageQueue**.

- It stores:
  - `Message` objects
  - `Runnable`s

They’re processed sequentially, FIFO

## Handler: Your Messenger Pigeon

- A Handler is a bridge:
  - You post work
  - It lands in a MessageQueue
  - The Looper executes it on its thread

### Posting to the main thread

```Java
Handler handler = new Handler(Looper.getMainLooper());
handler.post(() -> {
    textView.setText("UI updated");
});
```

### Delayed execution

```Java
handler.postDelayed(() -> {
    // runs after delay
}, 1000);
```

**Handler** = how you talk to a thread

**Looper** = keeps the thread alive

**MessageQueue** = stores work

## HandlerThread: Background Thread With a Brain

Creating your own Looper manually is ceremonial process.
Android gives you HandlerThread.

```Java
HandlerThread ht = new HandlerThread("Worker");
ht.start();

Handler handler = new Handler(ht.getLooper());
handler.post(() -> {
    // background work
});
```

## Main Thread + Background Thread Communication

- Classic pattern:
  1. Do work on background thread
  2. Post result to main thread

```Java
Handler mainHandler = new Handler(Looper.getMainLooper());

new Thread(() -> {
    String result = doWork();

    mainHandler.post(() -> {
        textView.setText(result);
    });
}).start();
```

## Why Not Update UI From Background Thread ?

- Because:
  - UI toolkit is not thread-safe
  - Race conditions
  - Half-drawn views

## Modern Replacements

### 1. Executor / ThreadPoolExecutor

For parallel work.

```Java
Executor executor = Executors.newSingleThreadExecutor();
executor.execute(() -> { /* background */ });
```

Still need a Handler to go back to main thread.

### 2. Coroutines (Kotlin)

```Kotlin
lifecycleScope.launch {
    val data = withContext(Dispatchers.IO) {
        loadData()
    }
    textView.text = data
}
```

- Structured concurrency
- Lifecycle-aware
- Fewer leaks
- Less boilerplate

Under the hood, it still uses threads and loopers.
