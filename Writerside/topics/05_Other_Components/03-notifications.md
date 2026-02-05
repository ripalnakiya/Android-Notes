# Notifications

Notifications are a way for apps to **alert users about important events or 
information** even when the app is not actively in use.

## Manifest Permission

```xml
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
```

## Notification Channel

```Java
NotificationChannel notificationChannel = new NotificationChannel(CHANNEL_ID, "NEWS", NotificationManager.IMPORTANCE_DEFAULT);
notificationChannel.setDescription("Get latest news");

NotificationManager notificationManager = getSystemService(NotificationManager.class);
notificationManager.createNotificationChannel(notificationChannel);
```

## Create Notification

```Java
Intent intent = new Intent(this, MainActivity.class);
PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_IMMUTABLE);

// With Backstack
    // TaskStackBuilder taskStackBuilder = TaskStackBuilder.create(this);
    // taskStackBuilder.addNextIntentWithParentStack(intent);
    // PendingIntent pendingIntent = taskStackBuilder.getPendingIntent(1, PendingIntent.FLAG_IMMUTABLE);
// Also : add parentActivityName in manifest

Notification.Builder builder = new Notification.Builder(this, CHANNEL_ID)
        .setSmallIcon(R.drawable.home)
        .setContentTitle("Main Activity")
        .setContentText("This notification will redirect you to Main Activity")
        .setContentIntent(pendingIntent)
        .setAutoCancel(true);
```

## Show Notification

```Java
NotificationManager notificationManager = getSystemService(NotificationManager.class);
notificationManager.notify(id, notification);
```
