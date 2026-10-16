# Side Effects

[Official Docs](https://developer.android.com/develop/ui/compose/side-effects)

A side-effect is a change to the state of the app that happens outside the scope of a composable function. 
Due to composables' lifecycle and properties such as unpredictable recompositions, 
executing recompositions of composables in different orders, or recompositions that can be discarded, 
composables should ideally be side-effect free.

However, sometimes side-effects are necessary, for example, 
to trigger a one-off event such as showing a snackbar or navigate to another screen given a certain state condition. 
These actions should be called from a controlled environment that is aware of the lifecycle of the composable. 
In this page, you'll learn about the different side-effect APIs Jetpack Compose offers.

When you need to make changes to the state of the app, 
you should use the **Effect APIs** so that those **side effects are executed in a predictable manner**.

> An **effect** is a composable function that doesn't emit UI 
> and causes side effects to run **when a composition completes**.

> Make sure that the work you do in them is **UI related** and **doesn't break unidirectional data flow**.

## `LaunchedEffect`: run suspend functions in the scope of a composable

To perform work over the life of a composable and have the ability to call suspend functions, 
use the `LaunchedEffect` composable. 

When `LaunchedEffect` **enters the Composition**, it launches a coroutine with the block of code passed as a parameter. 

The coroutine will be cancelled if `LaunchedEffect` **leaves the composition**. 

If `LaunchedEffect` is recomposed with different keys (see the [Restarting Effects](#restarting-effects) section below), 
the existing coroutine will be cancelled and the new suspend function will be launched in a new coroutine.

> If the Composable recomposes without changing the key of `LaunchedEffect`,
> the coroutine **will not be cancelled**.

## `rememberCoroutineScope`: obtain a composition-aware scope to launch a coroutine outside a composable

As `LaunchedEffect` is a _composable function_, it can only be used inside other composable functions. 

In order to launch a coroutine outside a composable, 
but scoped so that it will be automatically cancelled once it leaves the composition, use `rememberCoroutineScope`.

`rememberCoroutineScope` is a _composable function_ 
that returns a `CoroutineScope` bound to the point of the Composition where it's called. 
The scope will be cancelled when the call leaves the Composition.

## `rememberUpdatedState`: reference a value in an effect that shouldn't restart if the value changes

`LaunchedEffect` restarts when one of the key parameters changes. 

However, in some situations you might want to capture a value in your effect that, if it changes, 
you do not want the effect to restart. 

In order to do this, it is required to use `rememberUpdatedState` to create a reference to this value 
which can be captured and updated. 

This approach is helpful for effects that contain long-lived operations 
that may be expensive or prohibitive to recreate and restart.

For example, suppose your app has a `LandingScreen` that disappears after some time. 
Even if `LandingScreen` is recomposed, the effect that waits for some time and notifies 
that the time passed shouldn't be restarted:

```kotlin
@Composable
fun LandingScreen(onTimeout: () -> Unit) {

    // This will always refer to the latest onTimeout function that LandingScreen was recomposed with
    val currentOnTimeout by rememberUpdatedState(onTimeout)

    // Create an effect that matches the lifecycle of LandingScreen.
    // If LandingScreen recomposes, the delay shouldn't start again.
    LaunchedEffect(true) {
        delay(SplashWaitTimeMillis)
        currentOnTimeout()
    }

    /* Landing screen content */
}
```

To create an effect that matches the lifecycle of the call site, 
a never-changing constant like `Unit` or `true` is passed as a parameter.
In the code above, `LaunchedEffect(true)` is used. 

To make sure that the `onTimeout` lambda always contains the latest value that `LandingScreen` was recomposed with, 
`onTimeout` needs to be wrapped with the `rememberUpdatedState` function. 

The returned `State`, `currentOnTimeout` in the code, should be used in the effect.

## `DisposableEffect`: effects that require cleanup

For side effects that need to be _cleaned up_ after the keys change 
or if the composable leaves the Composition, use `DisposableEffect`. 

If the `DisposableEffect` keys change, the composable needs to dispose (do the cleanup for) its current effect, 
and reset by calling the effect again.

A `DisposableEffect` must include an `onDispose` clause as the final statement in its block of code. 
Otherwise, the IDE displays a build-time error.

## `SideEffect`: publish Compose state to non-Compose code

To share Compose state with objects not managed by Compose, use the `SideEffect` composable.

A `SideEffect` runs after **every recomposition** when invoked without any keys. 

To only perform the **effect once**, use an overload of `SideEffect` that accepts a key. 

To launch an **ongoing task spanning potentially many recompositions**, see `LaunchedEffect`.

> Note: For all overloads of `SideEffect`, 
> the effect is executed after all `DisposableEffect` and `RememberObserver` callbacks are dispatched. 
> 
> Effects are executed in the **order that they appear in the composition hierarchy** (following an in-order traversal).

## `produceState`: convert non-Compose state into Compose state

`produceState` launches a coroutine scoped to the Composition that can push values into a returned `State`. 

Use it to convert non-Compose state into Compose state, 
for example bringing external subscription-driven state such as `Flow`, `LiveData`, or `RxJava` into the Composition.

The producer is launched when `produceState` enters the Composition, 
and will be cancelled when it leaves the Composition. 

The returned `State` conflates; setting the same value won't trigger a recomposition.

## `derivedStateOf`: convert one or multiple state objects into another state

In Compose, recomposition occurs each time an observed state object or composable input changes. 
A state object or input may be changing more often than the UI actually needs to update, 
leading to unnecessary recomposition.

You should use the `derivedStateOf` function when 
your inputs to a composable are changing more often than you need to recompose. 

This often occurs when something is frequently changing, such as a scroll position, 
but the composable only needs to react to it once it crosses a certain threshold. 

`derivedStateOf` creates a new Compose state object you can observe that only updates as much as you need. 
In this way, it acts similarly to the Kotlin Flow `distinctUntilChanged()` operator.

## `snapshotFlow`: convert Compose's State into Flows

Use `snapshotFlow` to convert `State<T>` objects into a cold Flow.

## Restarting effects

Some effects in Compose, like `LaunchedEffect`, `produceState`, or `DisposableEffect`, 
take a variable number of arguments, keys, 
that are used to cancel the running effect and start a new one with the new keys.

As a rule of thumb, mutable and immutable variables used in the effect block of code 
should be added as parameters to the effect composable. 

Apart from those, more parameters can be added to force restarting the effect. 

If the change of a variable shouldn't cause the effect to restart, 
the variable should be wrapped in `rememberUpdatedState`. 

If the variable never changes because it's wrapped in a remember with no keys, 
you don't need to pass the variable as a key to the effect.

### Constants as keys

You can use a constant like `true` as an effect key to make it **follow the lifecycle of the call site**. 

There are valid use cases for it, like the `LaunchedEffect` example shown above. 

However, before doing that, think twice and make sure that's what you need.
