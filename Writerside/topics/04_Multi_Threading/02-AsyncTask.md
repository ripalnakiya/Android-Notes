# AsyncTask

**Starting AsyncTask**

```Java
ExampleAsyncTask task = new ExampleAsyncTask(this);
task.execute(10);
```

**AsyncTask**

- There are four methods in AsyncTask:
  - onPreExecute()
  - doInBackground()
  - onProgressUpdate()
  - onPostExecute()

- Three Parameters of AsyncTask:
  - Params: Type of the parameters sent to the task upon execution
  - Progress: Type of the progress units published during the background computation
  - Result: Type of the result of the background computation

```Java
private static class ExampleAsyncTask extends AsyncTask<Integer, Integer, String> {
    private WeakReference<MainActivity> activityWeakReference ;
    ExampleAsyncTask(MainActivity activity) {
        activityWeakReference = new WeakReference<MainActivity>(activity);
    }

    // This method will be executed on UI thread
    // Therefore we can access the UI view (progressBar), directly
    // But since It is a Non-static Inner class, It holds reference to the MainActivity Class, (May Cause Memory Leaks e.g. When Device is Rotated)
    // So to Solve this problem, we can either make this as `static` class or make this as separate top level class
    // But when we make this class as static, we cannot access the UI view directly
    // And to Solve this issue, we can use Weak Reference in the ExampleAsyncTask class
    @Override
    protected void onPreExecute() {
        super.onPreExecute();
//           progressBar.setVisibility(View.VISIBLE);

        // But we need strong reference to access the UI view
        // So we'll get strong reference for the particular function only
        MainActivity activity = activityWeakReference.get();

        // activityWeakReference.get() might return null, if MainActivity is destroyed
        if (activity == null || activity.isFinishing())
            return;

        activity.progressBar.setVisibility(View.VISIBLE);

        // To summerize, we cannot keep a strong reference in the scope of a class
        // otherwise it could cause memory leaks
        // (We get strong reference to the MainActivity by default when we create Non-static Inner class in it)
    }

    // This method will be executed on background thread
    // Return type of this method is the same as Result type mentioned in the <>
    @Override
    protected String doInBackground(Integer... integers) {
        for (int i = 0; i <integers[0]; i++) {
            int percentage = (i * 100) / integers[0];

            // This method will call onProgressUpdate()
            publishProgress(percentage);
            // publishProgress() will be called every one second

            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        return "Finished";
    }
    // The return value is passed to the onPostExecute()

    // This method will be executed on UI thread
    @Override
    protected void onProgressUpdate(Integer... values) {
        super.onProgressUpdate(values);
//           progressBar.setProgress(values[0]);

        MainActivity activity = activityWeakReference.get();
        if (activity == null || activity.isFinishing())
            return;
        activity.progressBar.setProgress(values[0]);
    }

    // This method will be executed on UI thread
    @Override
    protected void onPostExecute(String s) {
        super.onPostExecute(s);
//           Toast.makeText(MainActivity.this, s, Toast.LENGTH_SHORT).show();
//           progressBar.setProgress(0);
//           progressBar.setVisibility(View.INVISIBLE);

        MainActivity activity = activityWeakReference.get();
        if (activity == null || activity.isFinishing())
            return;

        Toast.makeText(activity, s, Toast.LENGTH_SHORT).show();
        activity.progressBar.setProgress(0);
        activity.progressBar.setVisibility(View.INVISIBLE);
    }
}
```

## Summary

- Easy
- Leaky
- Lifecycle-unaware
- Deprecated
