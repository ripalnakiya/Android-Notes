# Data Binding
<show-structure depth="2"/>

The Data Binding Library is a support library that allows you to bind UI components in your layouts 
to data sources in your app using a **declarative format** rather than programmatically.

Layouts are often defined in activities with code that calls UI framework methods. 

For example, the code below calls `findViewById()` to find a `TextView` widget and bind it to the `userName` property of the `viewModel` variable:

```kotlin
findViewById<TextView>(R.id.sample_text).apply {
    text = viewModel.userName
}
```

The following example shows how to use the Data Binding Library to assign text to the widget directly in the layout file. 
This removes the need to call any of the Java code shown above. 

Note the use of `@{}` syntax in the assignment expression:

```XML
<TextView
    android:text="@{viewmodel.userName}" />
```

Binding components in the layout file lets you remove many UI framework calls in your activities, 
making them simpler and easier to maintain. 

This can also improve your app's performance (removes the need of expensive `findViewById()`)
and help prevent memory leaks and null pointer exceptions.

## Setup

To get started with data binding, 
download the library from the **Support Repository** in the Android SDK manager.

To configure your app to use data binding, 
enable the dataBinding build option in your build.gradle file in the app module, 
as shown in the following example:

```kotlin
android {
    ...
    buildFeatures {
        dataBinding true
    }
}
```

### Configure the layout file

Original Layout file:

```XML
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <!-- Other Views -->

    </LinearLayout>
```

Wrap your original layout file in `<layout>...</layout>` tags.

> namespaces are always with parent tag

```XML
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools">

    <LinearLayout
            android:id="@+id/main"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:orientation="vertical"
            tools:context=".MainActivity">
        
        <!-- Other Views -->
        
    </LinearLayout>
</layout>
```

### Use DataBinding

```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
    }
}
```

## Basic Example

```XML
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- Define a variable that you want to use in your XML -->
    <data>
        <variable
            name="quote"
            type="com.ripalnakiya.databinding.QuoteModel" />
    </data>

    <LinearLayout
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <!-- Access that variable directly -->
        <TextView
            android:id="@+id/quoteText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{quote.text}"
            android:textSize="16sp" />

        <!-- Access that variable directly -->
        <TextView
            android:id="@+id/authorAuthor"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="20dp"
            android:text="@{quote.author}"
            android:textSize="14sp" />

    </LinearLayout>
</layout>
```

Make sure to set that variable from activity.

```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        
        // set the XML variable
        binding.quote = QuoteModel("This is a Quote.", "Author's Name")
    }
}
```

## DataBinding with ViewModel

We can use `ViewModel` in activity and observe the `LiveData`, and set views using the binding.
But this is a manual process.

We can directly access the `ViewModel` in our DataBinding layout file, 
and set the `LiveData` to the views directly, without writing intermediary code in activity.

### Manual Process

```XML
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <LinearLayout
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <TextView
            android:id="@+id/quoteText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginBottom="20dp"
            android:text="Hello World!" />

        <Button
            android:id="@+id/updateButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Update" />

    </LinearLayout>

</layout>
```

```kotlin
class MainViewModel: ViewModel() {
    val quoteLiveData = MutableLiveData("This is First Quote")

    fun updateQuote() {
        quoteLiveData.value = "This is Second Quote"
    }
}
```

```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var binding: ActivityMainBinding
    lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        viewModel = ViewModelProvider(this)[MainViewModel::class.java]
        
        // observing in activity, and then setting the views manually
        viewModel.quoteLiveData.observe(this) {
            binding.quoteText.text = it
        }
        
        binding.updateButton.setOnClickListener {
            viewModel.updateQuote()
        }
    }
}
```

### Using DataBinding features

```XML
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="viewModel"
            type="com.ripalnakiya.databindingviewmodel.MainViewModel" />
    </data>

    <LinearLayout
        android:id="@+id/main"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <!-- binding LiveData -->
        <TextView
            android:id="@+id/quoteText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginBottom="20dp"
            android:text="@{viewModel.quoteLiveData}" />
        <!--
        Note: when observing LiveData we need a lifecycleOwner,
        so that LiveData can know whom to send the updates (only active components).

        But we can't set the lifecycleOwner from XML, we need to do that from activity.
        -->

        <!-- binding a function call -->
        <Button
            android:id="@+id/updateButton"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{() -> viewModel.updateQuote()}"
            android:text="Update" />

    </LinearLayout>

</layout>
```

```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var binding: ActivityMainBinding
    lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        viewModel = ViewModelProvider(this)[MainViewModel::class.java]

        binding.viewModel = viewModel
        binding.lifecycleOwner = this // setting the lifecycleOwner to use LiveData in XML
    }
}
```
