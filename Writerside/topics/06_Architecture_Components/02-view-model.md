# ViewModel
<show-structure depth="2"/>

- Model for your views, such as Activity or Fragment
- View Models are **lifecycle aware**
- Data required for your screen is stored at one place i.e. ViewModel.
  It may involve formatting that data in a particular format, accumulating data, 
  any logic in displaying this data in your UI.

## ViewModel Lifecycle

![viewmodel-lifecycle.png](viewmodel-lifecycle.png)

## Example

```kotlin
class MainViewModel : ViewModel() {
  private var count = 0

  fun getCount() = count.toString()

  fun increaseCount() {
    count++
  }
}

class MainActivity : AppCompatActivity() {

    private lateinit var counterText: TextView
    private lateinit var increaseButton: Button

    private lateinit var viewModel: MainViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // get ViewModel instance
        viewModel = ViewModelProvider(/*Lifecycle Owner*/ this)[MainViewModel::class.java]

        counterText = findViewById(R.id.countText)
        counterText.text = viewModel.getCount()

        increaseButton = findViewById(R.id.increaseButton)
        increaseButton.setOnClickListener {
            viewModel.increaseCount()
            counterText.text = viewModel.getCount()
        }
    }
}
```

Count will survive configuration changes.

## ViewModelFactory

It is used when we want to pass a parameter to our ViewModel instance creation.

```kotlin
class MainViewModel(initialCount: Int) : ViewModel() {
    private var count = initialCount

    fun getCount() = count.toString()

    fun increaseCount() {
        count++
    }
}

class MainViewModelFactory(private val initialCount: Int): ViewModelProvider.Factory {

  override fun <T : ViewModel> create(modelClass: Class<T>): T {
    return MainViewModel(initialCount) as T
  }
}

class MainActivity : AppCompatActivity() {

  private lateinit var viewModel: MainViewModel

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    // Notice: we only added ViewModelFactory instance in ViewModelProvider
    viewModel = ViewModelProvider(/*Lifecycle Owner*/ this, MainViewModelFactory(10))[MainViewModel::class.java]
  }
}
```

> If you're passing `Context` to the `ViewModel`, pass **application context** instead of **activity context**,
> Because activity may get destroyed in configuration changes, and leak the memory.
