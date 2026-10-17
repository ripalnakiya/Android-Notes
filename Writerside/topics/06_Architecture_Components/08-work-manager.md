# WorkManager
<show-structure depth="2"/>

WorkManager features a robust scheduling mechanism that lets tasks persist across app restarts and device reboots.

WorkManager is the recommended library for persistent work. 
Scheduled work is guaranteed to execute sometime after its `Constraints` are met. 
WorkManager allows observation of work status and the ability to create complex chains of work.

WorkManager uses an underlying job dispatching service when available based on the following criteria:
- Uses JobScheduler for API 23+
- Uses a custom AlarmManager + BroadcastReceiver implementation for API 14-22

## Types of work

WorkManager handles three types of work:
- **Immediate:** Tasks that must begin immediately and complete soon. May be expedited.
- **Long Running:** Tasks which might run for longer, potentially longer than 10 minutes.
- **Deferrable:** Scheduled tasks that start at a later time and can run periodically.

![Work Manager](workmanager.svg){ height=700 }

| Type         | Periodicity          | How to access                                                                                              |
|--------------|----------------------|------------------------------------------------------------------------------------------------------------|
| Immediate    | One time             | `OneTimeWorkRequest` and `Worker`. For expedited work, call `setExpedited()` on your `OneTimeWorkRequest`. |
| Long Running | One time or periodic | Any `WorkRequest` or `Worker`. Call `setForeground()` in the Worker to handle the notification.            |
| Deferrable   | One time or periodic | `PeriodicWorkRequest` and `Worker`.                                                                        |

[Official Docs](https://developer.android.com/develop/background-work/background-tasks/persistent)

[Read more](https://medium.com/@appdevinsights/work-manager-android-6ea8daad56ee)
