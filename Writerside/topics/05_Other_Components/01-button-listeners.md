# Button Listeners

## 1. Anonymous Inner Class

```Java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            // DO SOMETHING
        }
    });
}
```

## 2. Lambda expressions

```Java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(view -> {
    // DO SOMETHING
    });
}
```

## 3. Method Reference

```Java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(this::onClick);
}

private void onClick(View v) {
    int id = v.getId();
    // DO SOMETHING
}
```


## 4. Interface Object

```Java
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

## 5. Interface Object (Lambda)

```Java
public class MainActivity extends AppCompatActivity {
    buttonStart.setOnClickListener(onClickListener);
}

View.OnClickListener onClickListener = v -> {
    int id = v.getId();
    // DO SOMETHING
};
```

## 6. Interface in Activity

Implements `View.OnClickListener` interface.

```Java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    buttonStart.setOnClickListener(this);
}

@Override
public void onClick(View v) {
    int id = v.getId();
    // DO SOMETHING
}
```