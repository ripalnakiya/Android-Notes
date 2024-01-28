# Button Listeners

## Method 1

> Using Anonymous Inner Class

```java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            // DO SOMETHING
        }
    });
}
```

## Method 2

> Using Lambda expressions

```java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(view -> {
    // DO SOMETHING
    });
}
```

## Method 3

> Using Method Reference

```java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(this::onClick);
}

private void onClick(View v) {
    int id = v.getId();
    // DO SOMETHING
}
```

## Method 4

> Using Activity as Listener (Implements View.OnClickListener)

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    buttonStart.setOnClickListener(this);
}

@Override
public void onClick(View v) {
    int id = v.getId();
    // DO SOMETHING
}
```

## Method 5

> Using View.OnClickListener as Listener

```java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(onClickListener);
}

View.OnClickListener onClickListener = new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        int id = v.getId();
        // DO SOMETHING
    }
};
```

## Method 6

> Using View.OnClickListener as Listener (Lambda)

```java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(onClickListener);
}

View.OnClickListener onClickListener = v -> {
    int id = v.getId();
    // DO SOMETHING
};
```
