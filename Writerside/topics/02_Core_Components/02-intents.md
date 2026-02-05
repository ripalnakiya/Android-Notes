# Intent

It is a **message object** used to request an operation or to communicate between components in an Android application.

> They are message objects used to request an action from another app component.

- With an Intent, we can:
  - Start an Activity
  - Start or bind to a Service
  - Send a Broadcast
  - Pass data between components

## Explicit Intent

We specify exactly which component to start in our app or in the other app.

```Java
Intent intent = new Intent(this, SecondActivity.class);
startActivity(intent);
```

```Java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.setData(Uri.parse("https://www.youtube.com/watch?v=2hIY1xuImuQ"));
intent.setPackage("com.google.android.youtube");
startActivity(intent);
```

## Implicit Intent

You specify **what you want done**, not **who does** it.

- Used when:
  - You want another app to handle the action
  - Multiple apps could respond

It allows the OS to **determine which component to start** by 
comparing the contents of the `intent` to the `intent filters` 
declared in the `manifest file` of other apps on the device.

Android shows a chooser or picks a default.

```Java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SEND);

// This shows the apps that accept an Intent Sending Text
intent.setType("text/plain");

intent.putExtra(Intent.EXTRA_EMAIL, new String[]{"test@test.test"});
intent.putExtra(Intent.EXTRA_SUBJECT, "Test Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Hello, This is Test Mail");

// Chooser shows list of all the apps that can handle text sharing
String chooserTitle = "Share this Text with";
Intent chooser = Intent.createChooser(intent, chooserTitle);

// startActivity(intent);   Since, we have a chooser, we can use it
startActivity(chooser);
```

### Intent Filters

They are used for **accepting an implicit intent**.

Components declare intent-filters in `AndroidManifest.xml`.

```xml
<intent-filter>
    <action android:name="android.intent.action.SEND"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <data android:mimeType="text/plain"/>
</intent-filter>
```

```Java
Intent intent = getIntent();

String Iemail = Arrays.toString(intent.getStringArrayExtra(Intent.EXTRA_EMAIL)) ;
String Isubject = intent.getStringExtra(Intent.EXTRA_SUBJECT);
String Ibody = intent.getStringExtra(Intent.EXTRA_TEXT);
```

## Pending Intent

A PendingIntent in android **is a token** that 
you give to a **foreign application** (e.g., NotificationManager, AlarmManager, AppWidgetManager, etc.) 
to allow it to execute a specific piece of code on your behalf.

`PendingIntent` is same as `Intent`, 
but to be triggered at later point in time by someone else (Operating System).

```Java
Intent intent = new Intent(context, MyActivity.class);
PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);
```

> We can create a PendingIntent using methods like `getActivity()`, `getService()`, or `getBroadcast()` in the `PendingIntent` class.
