# LiveData
<show-structure depth="2"/>

`LiveData` is an observable data holder class. Unlike a regular observable, 
`LiveData` is lifecycle-aware, meaning it respects the lifecycle of other app components, such as activities, fragments, or services. 

This awareness ensures `LiveData` only updates app component observers that are in an active lifecycle state.

LiveData considers an observer, which is represented by the Observer class, 
to be in an active state if its lifecycle is in the `STARTED` or `RESUMED` state. 

LiveData only notifies active observers about updates. 

Inactive observers registered to watch LiveData objects aren't notified about changes.

## Example

```kotlin
class MainViewModel: ViewModel() {
    private val _factsLiveData = MutableLiveData<String>("This is a Fact")

    val factsLiveData: LiveData<String>
        get() = _factsLiveData

    fun updateText() {
        _factsLiveData.value = "Another Fact"
    }
}

class MainActivity : AppCompatActivity() {
    private lateinit var viewModel: MainViewModel

    private val displayText: TextView
        get() = findViewById(R.id.displayText)

    private val updateButton: Button
        get() = findViewById(R.id.updateButton)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        viewModel = ViewModelProvider(this)[MainViewModel::class.java]

        viewModel.factsLiveData.observe(this) {
            displayText.text = it // updates the TextView whenever value of factsLiveData changes
        }

        updateButton.setOnClickListener { viewModel.updateText() }
    }
}
```
