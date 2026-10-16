# State Hoisting
<show-structure depth="2"/>

State hoisting means moving state ownership to the caller so a composable becomes more reusable and testable.

```kotlin
@Composable
fun TipTimeLayout(modifier: Modifier = Modifier) {
    Column(
        modifier = Modifier
            .padding(horizontal = 40.dp)
            .verticalScroll(rememberScrollState())
            .safeDrawingPadding(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(
            text = stringResource(R.string.calculate_tip),
            modifier = Modifier
                .padding(bottom = 16.dp, top = 40.dp)
                .align(alignment = Alignment.Start)
        )
        EditNumberField(modifier = Modifier.padding(bottom = 32.dp).fillMaxWidth())
        Text(
            text = stringResource(R.string.tip_amount, "$0.00"),
            style = MaterialTheme.typography.displaySmall
        )
        Spacer(modifier = Modifier.height(150.dp))
    }
}

@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
  var amountInput by remember { mutableStateOf("") }

  val amount = amountInput.toDoubleOrNull() ?: 0.0
  val tip = calculateTip(amount)

  TextField(
    label = { Text(stringResource(R.string.bill_amount)) },
    value = amountInput,
    onValueChange = { amountInput = it },
    singleLine = true,
    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
    modifier = modifier,
  )
}
```

You need to access the `amountInput` variable in the `TipTimeLayout()` function to calculate and display the tip amount, 
but the `amountInput` variable is the state of the text field defined in the `EditNumberField()` composable function, 
so you can't call it from the `TipTimeLayout()` function yet. 

This image illustrates the structure of the code:

![state-hoisting-1.png](state-hoisting-1.png) { height=400 }

This structure won't let you display the tip amount in the new `Text` composable 
because the `Text` composable needs to access the `amount` variable calculated from the `amountInput` variable. 
You need to expose the `amount` variable to the `TipTimeLayout()` function. 

This image illustrates the desired code structure, which makes the `EditNumberField()` composable stateless:

![state-hoisting-2.png](state-hoisting-2.png) { height=400 }

This pattern is called state hoisting. 

You _hoist_, or lift, the state from a composable to make it stateless.

> A stateless composable is a composable that doesn't store its own state. 
> 
> It displays whatever state it's given as input arguments.

You should hoist the state when you need to:
- Share the state with multiple composable functions.
- Create a stateless composable that can be reused in your app.

When you extract state from a composable function, the resulting composable function is called stateless. 
That is, composable functions can be made stateless by extracting state from them.

- A _stateless_ composable is a composable that doesn't have a state, meaning it doesn't hold, define, or modify a new state. 
- On the other hand, a _stateful_ composable is a composable that owns a piece of state that can change over time.

State hoisting is a pattern of moving state to its caller to make a component stateless.

When applied to composables, this often means introducing two parameters to the composable:
- A `value: T` parameter, which is the current value to display.
- An `onValueChange: (T) -> Unit` – callback lambda, which is triggered when the value changes so that the state can be updated elsewhere, such as when a user enters some text in the text box.

```kotlin
@Composable
fun TipTimeLayout(modifier: Modifier = Modifier) {
    var amountInput by remember { mutableStateOf("") }

    val amount = amountInput.toDoubleOrNull() ?: 0.0
    val tip = calculateTip(amount)

    Column(
        modifier = Modifier
            .padding(horizontal = 40.dp)
            .verticalScroll(rememberScrollState())
            .safeDrawingPadding(),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.Center
    ) {
        Text(
            text = stringResource(R.string.calculate_tip),
            modifier = Modifier
                .padding(bottom = 16.dp, top = 40.dp)
                .align(alignment = Alignment.Start)
        )
        EditNumberField(
            value = amountInput,
            onValueChange = { amountInput = it },
            modifier = Modifier.padding(bottom = 32.dp).fillMaxWidth()
        )
        Text(
            text = stringResource(R.string.tip_amount, "$0.00"),
            style = MaterialTheme.typography.displaySmall
        )
        Spacer(modifier = Modifier.height(150.dp))
    }
}

@Composable
fun EditNumberField(value: String, onValueChange: (String) -> Unit, modifier: Modifier = Modifier) {
    TextField(
        label = { Text(stringResource(R.string.bill_amount)) },
        value = value,
        onValueChange = onValueChange,
        singleLine = true,
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
        modifier = modifier,
    )
}
```

This makes `EditNumberField` stateless. 

You hoisted the UI state to its ancestor, `TipTimeLayout()`. 

The `TipTimeLayout()` is the state(`amountInput`) owner now.
