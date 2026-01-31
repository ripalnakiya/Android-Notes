# Fragments

A Fragment **represents a reusable portion** of your app's UI.

A fragment d**efines and manages its own layout**, has its own **lifecycle**, and can **handle its own input events**.

Fragments can't live on their own. They **must be hosted by an activity** or **another fragment**.

Fragments introduce **modularity and reusability into your activity’s UI** by letting you divide the UI into discrete chunks.

![Fragment Lifecycle](../images/fragment_lifecycle.png)

## Creating and Using Fragments

Create a container in Activity's layout file to host the fragment.

```xml
<FrameLayout
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

Use FragmentManager to add, replace, or remove fragments from the activity.

```Java
// Get Fragment Manager
FragmentManager fragmentManager = getSupportFragmentManager();

// Begin Fragment Transaction
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

// Perform Fragment Transaction
if (flag) {
    fragmentTransaction.add(R.id.container, fragment);
} else {
    fragmentTransaction.replace(R.id.container, fragment);
}

// Add the Fragment Transaction to the back stack
fragmentTransaction.addToBackStack(null);

// Commit the Fragment Transaction
fragmentTransaction.commit();
```

## Data passing between Activity and Fragment using Bundle

**MainActivity.java**

```Java
FragmentManager fragmentManager = getSupportFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

FirstFragment fragment1 = new FirstFragment();

Bundle args = new Bundle();
args.putString("num", "10");
fragment1.setArguments(args);

fragmentTransaction.add(R.id.container1, fragment1);
fragmentTransaction.commit();
```

**FirstFragment.java -> onCreateView()**

```Java
String str = "";
if (getArguments() != null) {
    str = getArguments().getString("num");
}
```

## Scenario 1 - Fragment on Button Press in Activity

**App Started :**

- onCreate() : Activity
- onStart() : Activity
- onResume() : Activity

**Started Fragment :**

- onAttach() : Fragment
- onCreate() : Fragment
- onCreateView() : Fragment
- onStarted() : Fragment
- onResume() : Fragment

**Back Pressed :**

- onPause() : Fragment
- onStop() : Fragment
- onDestroyView() : Fragment
- onDestroy() : Fragment
- onDetach() : Fragment

**Again, Back Pressed :**

- onPause() : Activity
- onStop() : Activity
- onDestroy() : Activity

## Scenario 2 - Fragment with Activity

**App Started :**

- onCreate() : **Activity** | onAttack() : **Fragment**, onCreate() : **Fragment**, onCreateView() : **Fragment**
- onStart() : **Activity** | onStart() : **Fragment**
- onResume() : **Activity** | onResume() : **Fragment**

**Back Pressed :**

- onPause() : **Fragment** | onPause() : **Activity**
- onStop() : **Fragment** | onStop() : **Activity**
- onDestroyView() : **Fragment**, onDestroy() : **Fragment**, onDetach() : **Fragment** | onDestroy() : **Activity**

## Scenario 3 - Activity with Fragment and Configuration Change

**App Started :**

- onCreate() : **Activity** | onAttack() : **Fragment**, onCreate() : **Fragment**, onCreateView() : **Fragment**
- onStart() : **Activity** | onStart() : **Fragment**
- onResume() : **Activity** | onResume() : **Fragment**

**Configuration Change :**

- onPause() : **Fragment** | onPause() : **Activity**
- onStop() : **Fragment** | onStop() : **Activity**
- onDestroyView() : **Fragment**, onDestroy() : **Fragment**, onDetach() : **Fragment** | onDestroy() : **Activity**
- onCreate() : **Activity** | onAttack() : **Fragment**, onCreate() : **Fragment**, onCreateView() : **Fragment**
- onStart() : **Activity** | onStart() : **Fragment**
- onResume() : **Activity** | onResume() : **Fragment**

## Scenario 4 - A new Fragment is replaced with existing Fragment

**App Started :**

- onCreate() : **Activity** | onAttack() : **Fragment A**, onCreate() : **Fragment A**, onCreateView() : **Fragment A**
- onStart() : **Activity** | onStart() : **Fragment A**
- onResume() : **Activity** | onResume() : **Fragment A**

**Fragment B is replaced with Fragment A :**

- onPause() : **Fragment A**
- onStop() : **Fragment A**
- onAttach() : **Fragment B**
- onCreate() : **Fragment B**
- onCreateView() : **Fragment B**
- onStart() : **Fragment B**
- onDestoryView() : **Fragment A**
- onDestroy() : **Fragment A**
- onDetach() : **Fragment A**
- onResume() : **Fragment B**
