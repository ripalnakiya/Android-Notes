# Data Persistance

- File I/O
- Shared Preferences
- SQLite Database
- Content Providers

## File I/O

### Write to a file:

```Java
FileOutputStream fos = null;

fos = openFileOutput(FILE_NAME, MODE_PRIVATE);

fos.write(text.getBytes());

Toast.makeText(this, "Saved to " + getFilesDir() + "/" + FILE_NAME , Toast.LENGTH_SHORT).show();

if(fos != null) {
        fos.close();
}
```

### Read from a file:

```Java
FileInputStream fin = null;

fin = openFileInput(FILE_NAME);

InputStreamReader inputStreamReader = new InputStreamReader(fin);
BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
StringBuilder sb = new StringBuilder();

while((String text = bufferedReader.readLine()) != null) {
    sb.append(text).append("\n");
}

if(fin != null) {
    fin.close();
}
```

## Shared Preference

It is simple **key-value storage system** that allows you to **store and retrieve primitive data** types (such as boolean, float, int, long, String) in a persistent way.

SharedPreferences are commonly used for saving **application settings**, **user preferences**, and other small pieces of data that need to be persisted between app sessions.

### Writing to Shared Preference

```Java
SharedPreferences sharedPreferences = getSharedPreferences("login", MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPreferences.edit();
editor.putBoolean("flag", true);
editor.apply();
```

### Reading from Shared Preference

```Java
SharedPreferences sharedPreferences = getSharedPreferences("login", MODE_PRIVATE);
boolean check = sharedPreferences.getBoolean("flag", false);    // getBoolean(key, defaultValue)
```
