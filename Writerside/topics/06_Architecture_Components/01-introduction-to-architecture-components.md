# Introduction
<show-structure depth="2"/>

Android has component-based architecture - where you use different components like Activity, Fragment, Service etc.

It is Android OS that manages these component for you.

Till now, we have been writing code in our android activities or fragments for all purposes - 
whether it is data fetching or storing data in SQLite.

This is where the **Problem** lies.

Benefits:
- Separation of concerns: View is handled by one component, Data is handled by another component
- Data Driven Applications: Data is ultimate source of truth, and UI depends on it

![android-jetpack.png](android-jetpack.png)

Android Jetpack is collection of libraries helps build robust, testable and maintainable android apps.
- Less boilerplate code - Room, Navigation Components, Paging etc.
- High quality production ready apps

## Lifecycle Aware Components

Most of the code is written inside Activity Lifecycle methods -
`onCreate`, `onResume`, `onPause` etc.

Due to this Activity has multiple responsibilities.

But there are scenarios where we want to take actions based on the activity lifecycle. For example,
- Access user's location
- Playing video
- Downloading images

### Lifecycle Owner & Lifecycle Observer

```kotlin
class MainActivity : AppCompatActivity() { // Lifecycle Owner
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        lifecycle.addObserver(MyObserver())
        lifecycle.addObserver(MyEventObserver())

        Log.d(TAG, "onCreate: ")
    }
}

class MyObserver: DefaultLifecycleObserver { // Lifecycle Observer
    override fun onCreate(owner: LifecycleOwner) {
        super.onCreate(owner)
        Log.d(TAG, "onCreate: ")
    }
    
    // implement other lifecycle callbacks
}

class MyEventObserver: LifecycleEventObserver { // Lifecycle Observer
    override fun onStateChanged(source: LifecycleOwner, event: Lifecycle.Event) {
        // event: contains the enum constant of the lifecycle even that happened
        Log.d(TAG, "onStateChanged: source: $source | event: $event")
    }
}
```
