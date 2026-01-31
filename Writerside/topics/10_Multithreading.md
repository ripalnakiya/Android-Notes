# Multithreading

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
//                   runOnUiThread(new Runnable() {
//                       @Override
//                       public void run() {
//                           textView.setText(R.string.fifty_percent);
//                       }
//                   });

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
