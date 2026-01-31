# Intent

An **asynchronous message** called an intent activates three of the four component types: activities, services, and broadcast receivers.

It is a **messaging object** used to request an operation or to communicate between components in an Android application.

## Explicit Intent

An explicit intent is one that you use to launch a **specific app component**, such as a particular activity or service in our app or in the other app.

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

An implicit intent is one that the system uses to **determine which component to start** by comparing the contents of the intent to the intent filters declared in the manifest file of other apps on the device.

```Java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SEND);

// This shows the apps that accept an Intent Sending Text
intent.setType("text/plain");

intent.putExtra(Intent.EXTRA_EMAIL, new String[]{"ripalnakiya@gmail.com"});
intent.putExtra(Intent.EXTRA_SUBJECT, "Email Subject");
intent.putExtra(Intent.EXTRA_TEXT, "Hello, This is Sample Mail");

// Chooser shows list of all the apps that can handle text sharing
String chooserTitle = "Share this Text with";
Intent chooser = Intent.createChooser(intent, chooserTitle);

// startActivity(intent);
startActivity(chooser);
```

**Accepting an implicit intent**

Firstly, add this intent filter in the manifest file of the activity that you want to launch.

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

A PendingIntent in Android **is a token** that you give to a **foreign application** (e.g., NotificationManager, AlarmManager, AppWidgetManager, etc.) to allow it to execute a specific piece of code on your behalf.

Pending Intent is same as Intent, but to be triggered at later point in time by some one else (Operating System)

```Java
Intent intent = new Intent(context, MyActivity.class);
PendingIntent pendingIntent = PendingIntent.getActivity(context, 0, intent, 0);
```

> You can create a PendingIntent using methods like `getActivity()`, `getService()`, or `getBroadcast()` in the `PendingIntent` class.
