# AlarmManager

**Show the time picker dialog:**

```java
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

```java
Intent intent = new Intent(this, AlarmReceiver.class);
PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 1, intent, PendingIntent.FLAG_MUTABLE);

AlarmManager alarmManager = getSystemService(AlarmManager.class);
alarmManager.setExact(AlarmManager.RTC_WAKEUP, c.getTimeInMillis(), pendingIntent);
```

> Then we can set the onReceive() method in the AlarmReceiver class to show the notification.
>
> Or perform any other action.
