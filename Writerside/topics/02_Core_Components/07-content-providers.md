# Content Providers

A Content Provider is Android’s **standardized data-sharing layer**.

It is a **contract + IPC mechanism** that lets one app expose data to 
other apps in a controlled, permission-aware way.

## Why Content Providers

Apps are sandboxed.

- One app cannot directly read another app’s:
  - SQLite database
  - files
  - in-memory data

## Core Idea

A Content Provider exposes data via URIs, 
and clients interact using CRUD operations, not implementation details.

### 1. Authority

A unique identifier for the provider.

```
content://com.example.contactsprovider
```

### 2. URI structure

```
content://authority/path/[id]
```

Examples:

```
content://com.example.notesprovider/notes       → all rows
content://com.example.notesprovider/notes/42    → single row
```

This URI is the _API surface_ of your data.

### 3. CRUD methods

Every Content Provider implements these:

```Java
query()
insert()
update()
delete()
getType()
```

These are process-safe and run via Binder IPC.

Clients use `ContentResolver`, not the provider directly.

### 4. ContentResolver (client side)

Apps never talk to providers directly.

```Java
Cursor cursor = getContentResolver().query(
    uri,
    projection,
    selection,
    selectionArgs,
    sortOrder
);
```

## Cursor

Query results come back as a Cursor.

- Cursor is:
  - Lazy
  - Memory-efficient
  - IPC-friendly

You move row by row instead of loading everything at once.

## Permissions and security

### Manifest-level permissions

```xml
<provider
    android:name=".NotesProvider"
    android:authorities="com.example.notesprovider"
    android:exported="true"
    android:readPermission="com.example.READ_NOTES"
    android:writePermission="com.example.WRITE_NOTES" />
```

- You can:
  - Allow read only
  - Allow write only
  - Restrict access per operation

## System Content Providers

- ContactsProvider
- MediaStore
- CalendarProvider
- SettingsProvider
