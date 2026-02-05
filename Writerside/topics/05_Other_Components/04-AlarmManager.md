# AlarmManager

AlarmManager schedules time-based operations. You tell Android:

“At this time (or every X minutes), wake up and do this.”

## Typical uses

### 1. Run work at a specific time

- Examples:
  - Show a notification at 9 AM
  - Trigger a daily reminder
  - Schedule an event in the future

- This works even if:
  - Your app is killed
  - The user hasn’t opened it in days
  - The device was idle

### 2. Periodic tasks (with caveats)

- Sync data every few hours
- Refresh something daily

But Android now aggressively **batches and delays alarms** to save battery. 
Precision is optional unless you explicitly demand it.

### 3. Wake the device

AlarmManager can wake the device from sleep using wake-up alarms.

## How it works internally

- AlarmManager does not run code directly.
- It triggers a **PendingIntent**.
- That PendingIntent usually starts:
  - A BroadcastReceiver
  - A Service
  - An Activity (rare and annoying)

So AlarmManager is basically a time-based Intent launcher.

## Alarm Types

### 1. Inexact alarms (recommended)

- `setInexactRepeating()`
- System groups alarms together to save battery

Use when exact timing does not matter.

### 2. Exact alarms

- `setExact()`
- `setExactAndAllowWhileIdle()`

Use only when:
- Timing must be precise
- You accept battery drain
- On Android 12+, you may need `EXACT_ALARM` permission

### 3. Repeating alarms

Mostly discouraged now. 

The system prefers rescheduling alarms manually after each trigger.

## What AlarmManager is NOT good for

Long-running background work

Frequent background tasks

Network-heavy jobs

## Modern alternatives

Because developers abused AlarmManager into battery extinction.

- **WorkManager:** Best for deferrable background work. Respects system rules.
- **JobScheduler:** Lower-level, system-managed background jobs.

Rule of thumb:
- Exact time needed? AlarmManager
- Eventually is fine? WorkManager

## Example

**Show the time picker dialog:**

```Java
Calendar calendar = Calendar.getInstance();
int hour = calendar.get(Calendar.HOUR_OF_DAY);
int minute = calendar.get(Calendar.MINUTE);

TimePickerDialog timePickerDialog = new TimePickerDialog(MainActivity.this, new TimePickerDialog.OnTimeSetListener() {
    @Override
    public void onTimeSet(TimePicker view, int hourOfDay, int minute) {
        Calendar c = Calendar.getInstance();
        c.set(Calendar.HOUR_OF_DAY, hourOfDay);
        c.set(Calendar.MINUTE, minute);
        c.set(Calendar.MILLISECOND, 0);
        Log.d(TAG, "onTimeSet: ");
        setAlarm(c);
    }
}, hour, minute, false);

timePickerDialog.show();
```

**Set the alarm:**

```Java
Intent intent = new Intent(this, AlarmReceiver.class);
PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 1, intent, PendingIntent.FLAG_MUTABLE);

AlarmManager alarmManager = getSystemService(AlarmManager.class);
alarmManager.setExact(AlarmManager.RTC_WAKEUP, c.getTimeInMillis(), pendingIntent);
```

> Then we can set the onReceive() method in the AlarmReceiver class to show the notification.
>
> Or perform any other action.
