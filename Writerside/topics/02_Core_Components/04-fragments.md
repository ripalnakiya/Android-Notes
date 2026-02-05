# Fragments

- A Fragment:
  - Is a modular and reusable piece of UI
  - Has its own lifecycle
  - Is always hosted by an Activity
  - Can be added, removed, replaced at runtime

![Fragment Lifecycle](../../images/fragment_lifecycle.png)

## Creating fragments

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

## Data passing b/w Activity and Fragment

`MainActivity.java`

```Java
FragmentManager fragmentManager = getSupportFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();

FirstFragment fragment1 = new FirstFragment();

Bundle args = new Bundle();
args.putString("number", "10");
fragment1.setArguments(args);

fragmentTransaction.add(R.id.container1, fragment1);
fragmentTransaction.commit();
```

`FirstFragment.java`

```Java
onCreateView() {
    String str = "";
    if (getArguments() != null) {
        str = getArguments().getString("number");
    }
}
```
