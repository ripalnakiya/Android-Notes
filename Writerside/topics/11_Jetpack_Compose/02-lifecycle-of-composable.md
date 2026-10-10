# Lifecycle of Composable
<show-structure depth="2"/>

The UI of your app is initially built from running composable functions in a process called Composition.

When the state of your app changes, a recomposition is scheduled. 
Recomposition is when Compose re-executes the composable functions 
whose state might have changed and creates an updated UI. 
The Composition is updated to reflect these changes.

The only way to create or update a Composition is by its initial composition and subsequent recompositions.

> Composable functions have their own lifecycle that is independent of the Activity lifecycle. 
> 
> Its lifecycle is composed of the events: 
> - enters the Composition, 
> - recomposing 0 or more times, 
> - and then leaving the Composition.
>
> ![lifecycle-composition.png](lifecycle-composition.png){height=200}

## Recomposition

[Visit this codelab to understand Recomposition in detail](https://developer.android.com/codelabs/basic-android-kotlin-compose-using-state#0)

What happens if you want your UI to change while the app is running or as the user interacts with the app?
For example, what if you wanted to update the `amountInput` variable with the value entered by the user
and display it in the text box?

That's when you rely on a process called **recomposition** to update the Composition of the app.

> The _Composition_ is a description of the UI built by Compose when it executes composables.

> Compose apps call composable functions to transform data into UI.

If a state change happens, Compose re-executes the affected composable functions with the new state,
which creates an updated UI—this is called _recomposition_.
Compose schedules a _recomposition_ for you.

- When Compose runs your composables for the first time during _initial composition_,
  it keeps track of the composables that you call to describe your UI in a Composition.
- _Recomposition_ is when Compose re-executes the composables that may have changed
  in response to data changes and then updates the Composition to reflect any changes.

The Composition can only be produced by an _initial composition_ and updated by _recomposition_.
The only way to modify the Composition is through _recomposition_.

To do this, **Compose needs to know what state to track** so that it can schedule the recomposition when it receives an update.
In your case, it's the `result` variable, so whenever its value changes, Compose schedules a recomposition.

You use the `State` and `MutableState` types in Compose to make state in your app observable, or tracked, by Compose.
The `State` type is immutable, so you can only read the value in it, while the `MutableState` type is mutable.

You can use the `mutableStateOf()` function to create an observable `MutableState`.
It receives an initial value as a parameter that is wrapped in a `State` object, which then makes its `value` observable.

The value returned by the `mutableStateOf()` function:
- Holds state, which is the bill amount.
- Is mutable, so the value can be changed.
- Is observable, so Compose observes any changes to the value and triggers a recomposition to update the UI.

> Compose keeps track of each composable that reads state `value` properties
and triggers a recomposition when its `value` changes.

```kotlin
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
   var amountInput = mutableStateOf("0")
   TextField(
       value = amountInput.value,
       onValueChange = { amountInput.value = it },
       modifier = modifier
   )
}
```

When the user enters text in the text box,
the `onValueChange` callback is called and the `amountInput` variable is updated with the new value.

The `amountInput` state is tracked by Compose, so the moment that its value changes,
recomposition is scheduled and the `EditNumberField()` composable function is executed again.
In that composable function, the `amountInput` variable is reset to its initial `0` value.
Thus, the text box shows a `0` value.

With the code you added, **state changes** cause recompositions to be scheduled.

However, you need a way to preserve the value of the `amountInput`
variable across recompositions so that it's not reset to a `0` value each time
that the `EditNumberField()` function recomposes.

### Use remember function to save state

Composable methods can be called many times because of recomposition.
The composable resets its state during recomposition if it's not saved.

Composable functions can store an object across recompositions with the `remember`.

A value computed by the `remember` function is stored in the Composition during initial composition
and the stored value is returned during recomposition.

Usually `remember` and `mutableStateOf` functions are used together in composable functions
to have the state and its updates be reflected properly in the UI.

```kotlin
@Composable
fun EditNumberField(modifier: Modifier = Modifier) {
    var amountInput by remember { mutableStateOf("") }
    TextField(
        value = amountInput,
        onValueChange = { amountInput = it },
        modifier = modifier
    )
}
```

Adding the delegate's getter and setter imports lets you read and set `amountInput` without referring to the `MutableState`'s `value` property.

### Summary

During initial composition, `value` in the `TextField` is set to the initial value, which is an empty string.

When the user enters text into the text field, the `onValueChange` lambda callback is called,
the lambda executes, and the `amountInput.value` is set to the updated value entered in the text field.

The `amountInput` is the mutable state being tracked by the Compose, recomposition is scheduled.
The `EditNumberField()` composable function is recomposed.
Since you are using `remember { }`,
the change survives the recomposition and that is why the state is not re-initialized to `""`.

The `value` of the text field is set to the remembered value of `amountInput`.
The text field recomposes (redrawn on the screen with new value).

### Recomposition is optimistic

Recomposition starts whenever Compose thinks that the parameters of a composable might have changed.
Recomposition is _optimistic_, which means Compose expects to finish recomposition before the parameters change again.
If a parameter does change before recomposition finishes,
Compose might cancel the recomposition and restart it with the new parameter.

When **recomposition is cancelled**, Compose discards the UI tree from the recomposition.
If you have any side-effects that depend on the UI being displayed,
the **side-effect will be applied even if composition is cancelled**.
This can lead to inconsistent app state.

Verify that all composable functions and lambdas are idempotent and side-effect free
to handle optimistic recomposition.

### Composable functions can execute in any order

> Compose operates on the main thread, but it has been designed from the start with multithreading in mind,
> and we don't guarantee a single threaded model will remain in the future.
>
> Because of this, you should always write your composables as if they can be run on multiple threads.

## Example

In order for Compose to track and trigger a recomposition, it needs to know when state has changed. 
To indicate to Compose that it should track an object's state, 
the object needs to be of type `State` or `MutableState`. 
The `State` type is immutable and can only be read. 
A `MutableState` type is mutable and allows reads and writes.

To create the mutable variable revenue, you declare it using mutableStateOf. 0 is its initial default value.

```kotlin
var revenue = mutableStateOf(0)
```

While this is enough to have Compose trigger a recomposition when the revenue value changes, 
it is not enough to retain its updated value. 
Each time the composable is reexecuted, it will reinitialize the revenue value to its initial default value of `0`.

To instruct Compose to retain and reuse its value during recompositions, you need to declare it with the `remember` API.

```kotlin
var revenue by remember { mutableStateOf(0) }
```

If the value of `revenue` changes, Compose schedules all composable functions that read this value for recomposition.

While Compose remembers the revenue state during recompositions, 
it does not retain this state during a configuration change. 
For Compose to retain the state during a configuration change, you must use `rememberSaveable`.

## Use `rememberSaveable` to save values across configuration changes

You use the `rememberSaveable` function to save values that you need if Android OS destroys and recreates the activity.

To save values during recompositions, you need to use `remember`. 
Use `rememberSaveable` to save values during recompositions AND configuration changes.

Saving the value using `rememberSaveable` ensures that it is available when the activity is restored, if it is needed.

```kotlin
var revenue by rememberSaveable { mutableIntStateOf(0) }
```

## How would you detect excessive recomposition?

1. Use Layout Inspector in Android Studio
2. Add a recomposition counter in code
