# Permissions

App permissions help **support user privacy** by **protecting access** to the following:

- **Restricted data** : such as system state and users' contact information
- **Restricted actions** : such as connecting to a paired device and recording audio

## Types of permissions

### 1. Install-time permissions

Install-time permissions give 
your app **limited access to restricted data** or 
let your app **perform restricted actions that minimally affect the system** 
or other apps.

The system automatically **grants** your 
app the permissions **when the user installs your app**.

Android includes several sub-types of install-time permissions, 
including normal permissions and signature permissions.

#### Normal permissions

These permissions allow access to data and actions 
that extend beyond your app's sandbox but 
present very little risk to the user's privacy and the operation of other apps.

The system assigns the `normal` protection level to normal permissions.

#### Signature permissions

The system grants a signature permission to an app only when 
the app is signed by the same certificate as the app 
or the OS that defines the permission.

Applications that implement privileged services, 
such as autofill or VPN services, also make use of signature permissions.

The system assigns the `signature` protection level to signature permissions.

### 2. Runtime permissions

Runtime permissions give your app **additional access to restricted data** or 
let your app **perform restricted actions that more substantially affect the system** 
and other apps.

Therefore, you need to request runtime permissions in your app 
before you can access the restricted data or perform restricted actions.

Many runtime permissions access private user data, 
a special type of restricted data that includes potentially sensitive information. 

Examples of private user data include location and contact information.

The system assigns the `dangerous` protection level to runtime permissions.

### 3. Special permissions

Special permissions correspond to **particular app operations**.

Only the platform and OEMs can define special permissions.

> The platform and OEMs usually define special permissions **when they want to protect access to some powerful actions**, such as drawing over other apps.

The system assigns the `appop` protection level to special permissions.

## Runtime Permissions Example

**Single Permission:**

```Java
permissionLauncher.launch(Manifest.permission.POST_NOTIFICATIONS);


private final ActivityResultLauncher<String> permissionLauncher =
    registerForActivityResult(new ActivityResultContracts.RequestPermission(), isGranted -> {

    if (isGranted) {
        // Permission is granted. Continue the action or workflow in your app.
    } else {
        // Explain to the user that the feature is unavailable 
        // because the feature requires a permission that the user has denied.
    }
    }
);
```

**Grouped Permissions:**

```Java
private final String[] permissions = {
        Manifest.permission.READ_CONTACTS,
        Manifest.permission.WRITE_CONTACTS
};

permissionLauncher.launch(permissions);


private final ActivityResultLauncher<String[]> permissionLauncher =
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
