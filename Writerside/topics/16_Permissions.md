# Permissions

App permissions help **support user privacy** by **protecting access** to the following:

- **Restricted data** : such as system state and users' contact information
- **Restricted actions** : such as connecting to a paired device and recording audio

## Types of permissions

### Install-time permissions

Install-time permissions give your app **limited access to restricted data** or let your app **perform restricted actions that minimally affect the system** or other apps.

The system automatically **grants** your app the permissions **when the user installs your app**.

- Normal permissions

- Signature permissions

### Runtime permissions

Runtime permissions, also known as **dangerous permissions**, give your app **additional access to restricted data** or let your app **perform restricted actions that more substantially affect the system** and other apps.

Therefore, you need to **request runtime permissions** in your app **before you can access the restricted data or perform restricted actions**.

The system assigns the `dangerous` protection level to runtime permissions.

### Special permissions

Special permissions correspond to **particular app operations**.

Only the **platform and OEMs can define special permissions**.

> The platform and OEMs usually define special permissions **when they want to protect access to some powerful actions**, such as drawing over other apps.

The system assigns the `appop` protection level to special permissions.

## Protection levels

### Normal (Default)

The system automatically grants this type of permission to a requesting application at installation, without asking for the user's explicit approval, though the user always has the option to review these permissions before installing.

### Dangerous

A higher-risk permission that gives a **requesting application access to private user data or control over the device that can negatively impact the user**. Because this type of **permission introduces potential risk**, the system might not automatically grant it to the requesting application.

### Signature

A permission that the system grants **only if the requesting application is signed with the same certificate as the application that declared the permission**. If the certificates match, the system automatically grants the permission without notifying the user or asking for the user's explicit approval.

### knownSigner

A permission that the system grants **only if the requesting application is signed with an allowed certificate**. If the requester's certificate is listed, the system automatically grants the permission without notifying the user or asking for the user's explicit approval.

## Runtime Permissin Implementation

**Single Permission:**

```Java
requestPermissionLauncher.launch(Manifest.permission.POST_NOTIFICATIONS);

private final ActivityResultLauncher<String> requestPermissionLauncher =
    registerForActivityResult(new ActivityResultContracts.RequestPermission(), isGranted -> {

    if (isGranted) {
        // Permission is granted. Continue the action or workflow in your app.
    } else {
        // Explain to the user that the feature is unavailable because the feature requires a permission that the user has denied.
    }
});
```

**Grouped Permissions:**

```Java
private final String[] permissions = {
        Manifest.permission.READ_CONTACTS,
        Manifest.permission.WRITE_CONTACTS
};

requestPermissionsLauncher.launch(permissions);

 private final ActivityResultLauncher<String[]> requestPermissionsLauncher =
    registerForActivityResult(new ActivityResultContracts.RequestMultiplePermissions(), permissionsResult -> {

    // Check if all requested permissions are granted
    boolean allPermissionsGranted = true;
    for (boolean isGranted : permissionsResult.values()) {
        if (!isGranted) {
            allPermissionsGranted = false;
            break;
        }
    }

    if (allPermissionsGranted) {
        // All permissions are granted, perform your action
    } else {
        // Some or all permissions are denied, inform the user or handle accordingly
    }
});
```
