# Dialogs

> We can create Custom Dialogs by providing a layout to the Dialog box.

## Custom Dialog

```java
Dialog dialog = new Dialog(MainActivity.this);
dialog.setContentView(R.layout.custom_dialog_layout);
// Dialog boxes are closed by default when we click on the screen outside the dialog box.
// To cancel that functionality we use setCancelable();

dialog.setCancelable(false);
// We cannot directly access the button id, since button id doesn't belong to the MainActivity
// button id belongs to the dialog box, so we can access it using the object of Dialog

Button buttonOkay = dialog.findViewById(R.id.buttonOkay);
buttonOkay.setOnClickListener(view -> {
    Toast.makeText(MainActivity.this, "Okay pressed", Toast.LENGTH_SHORT).show();
    dialog.dismiss();
});

dialog.show();
```

# Custom Alert Dialog

```java
// Create a view for the dialog box by inflating the layout
View dialogView = LayoutInflater.from(context).inflate(R.layout.add_update_dialog, null);
// We can access the views of the dialog box using the dialogView object
EditText dialogTitle = dialogView.findViewById(R.id.dialogTitle);

AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);
builder.setView(dialogView);
        .setIcon(R.drawable.add);
        .setTitle("Add Record");
        .setMessage("Are you sure you want to add this record?");
        .setPositiveButton("Yes", (dialogInterface, i) -> Toast.makeText(MainActivity.this, "Added!", Toast.LENGTH_SHORT).show());
        .setNegativeButton("No", (dialogInterface, i) -> Toast.makeText(MainActivity.this, "Addition Canceled!", Toast.LENGTH_SHORT).show());

Dialog dialog = builder.create();
dialog.show();
```
