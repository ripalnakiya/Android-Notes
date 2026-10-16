# JNI
<show-structure depth="2"/>

[Refer Android Docs](https://developer.android.com/studio/projects/add-native-code)

## Setup in Android using NDK

### 1. Install NDK

Android Studio → SDK Manager → SDK Tools tab

- Enable:
  - ✅ NDK (Side by side)
  - ✅ CMake
  - ✅ LLDB

### 2. Create new C/C++ source files

To add new C/C++ source files to an existing project, proceed as follows:

1. If you don't already have a `cpp/` directory in the main source set of your app, create one as follows:
   1. Open the **Project** pane on the left side of the IDE and select the **Project** view from the menu.
   2. Navigate to **your-module > src**.
   3. Right-click on the **main** directory and select **New > Directory**.
   4. Enter cpp as the directory name and click **OK**.
2. Right-click the `cpp/` directory and select **New > C/C++ Source File**.
3. Enter a name for your source file, such as `native-lib`.
4. From the Type menu, select the file extension for your source file, such as `.cpp`.

After you add new C/C++ files to you project, 
you still need to **configure CMake** to include the files in your native library.

**Edit c++ file:**

```C++
#include <jni.h>
#include <string>

extern "C"
JNIEXPORT jstring JNICALL
Java_com_packagename_myapp_MainActivity_stringFromJNI(JNIEnv *env, jobject thiz) {
    std::string hello = "Hello from C++ 👀";
    return env->NewStringUTF(hello.c_str());
}
```

### 3. Create a CMake build script

> A CMake build script is a plain text file that you must name `CMakeLists.txt` 
> and includes commands CMake uses to build your C/C++ libraries. 
{style="note"}

To create a plain text file that you can use as your CMake build script, proceed as follows:

1. Open the **Project** pane from the left side of the IDE and select the **Project** view from the drop-down menu.
2. Right-click on the root directory of **your-module** and select **New > File**.
3. Enter "CMakeLists.txt" as the filename and click **OK**.

Add the below code in your `CMakeLists.txt`:

```CMake
# Sets the minimum version of CMake required to build your native library.
# This ensures that a certain set of CMake features is available to
# your build.

cmake_minimum_required(VERSION 3.4.1)

# Specifies a library name, specifies whether the library is STATIC or
# SHARED, and provides relative paths to the source code. You can
# define multiple libraries by adding multiple add_library() commands,
# and CMake builds them for you. When you build your app, Gradle
# automatically packages shared libraries with your APK.

add_library( # Specifies the name of the library.
             native-lib

             # Sets the library as a shared library.
             SHARED

             # Provides a relative path to your source file(s).
             src/main/cpp/native-lib.cpp )
```

### 4. Configure Gradle

To manually configure Gradle to link to your native library, 
you need to add the `externalNativeBuild` block to your module-level `build.gradle` file 
and configure it with either the `cmake` or `ndkBuild` block:

```Kotlin
android {
  ...
  defaultConfig {...}
  buildTypes {...}

  // Encapsulates your external native build configurations.
  externalNativeBuild {

    // Encapsulates your CMake build configurations.
    cmake {

      // Provides a relative path to your CMake build script.
      path = file("CMakeLists.txt")
    }
  }
}
```

### 5. Use it in Code

[Refer Android Docs](https://developer.android.com/ndk/samples/sample_hellojni)

<tabs>
    <tab title="Kotlin">
        <code-block lang="Kotlin">
companion object {
    init {
        System.loadLibrary("native-lib");
    }
}
        </code-block>
    </tab>
    <tab title="Java">
        <code-block lang="Java">
static {
    System.loadLibrary("native-lib");
}
        </code-block>
    </tab>
</tabs>

This function call loads the `.so` file upon application startup.


<tabs>
    <tab title="Kotlin">
        <code-block lang="Kotlin">
external fun stringFromJNI(): String
        </code-block>
    </tab>
    <tab title="Java">
        <code-block lang="Java">
public native String stringFromJNI();
        </code-block>
    </tab>
</tabs>

The `native` keyword in this method declaration tells 
the virtual machine that the function is in the shared library (that is, implemented on the native side).
