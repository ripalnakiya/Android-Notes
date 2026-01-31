# API Keys

## Using local.properties

We can use `local.properties` to store API keys and other sensitive information. This file is not checked into version control, so it is safe to store sensitive information here.

**local.properties**

```Properties
API_KEY=MyAPIKeyInLocalProperties
```

l̥
**build.gradle**

```Kotlin
// Make this section buildFeatures, and set buildConfig to true
buildFeatures {
    buildConfig = true
}
```

```Kotlin
defaultConfig {
    // Get values from local.properties
    val properties = Properties()   // Properties of java.util
    properties.load(project.rootProject.file("local.properties").inputStream())
    buildConfigField("String", "API_KEY", "\"${properties.getProperty("API_KEY")}\"")
}
```

l̥
**MainActivity.java**

```Java
String API_KEY = BuildConfig.API_KEY
```

## Using ProGuard

Another methods is to use **obfuscation**. This is a method of hiding the API key in the code. This is **not a very secure method**, but it is better than nothing.

It is done by using **ProGuard**. ProGuard is a tool that is used to obfuscate code. It is used to m**ake the code smaller and faster**. It is also used to make the code more secure.

In order to do this, **set isMinifyEnabled to true** in the build.gradle file.

**build.gradle**

```Kotlin
buildTypes {
    release {
        isMinifyEnabled = true
        proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
    }
}
```

## Using NDK

**Step 1** : Set Project view in the Android Studio

**Step 2** : go to app -> src -> main

**Step 3** : Right click on main, and select "Add C++ to module" from context menu

This will generate two files required

1. CMakeLists.txt
2. native-lib.cpp (other name can be used in place of 'native-lib')

And this will also add the following code in build.gradle

```Kotlin
defaultConfig {
    externalNativeBuild {
        cmake {
            cppFlags += ""
        }
    }
}
```

```Kotlin
externalNativeBuild {
    cmake {
        path = file("src/main/cpp/CMakeLists.txt")
        version = "3.22.1"
    }
}
```

**Step 4** : Add this code in MainActivity to dynamically load the C++ library into our application

```Java
static {
    System.loadLibrary("native-lib");
}
```

**Step 5** : Now write this line of code

```Java
public native String getApiKey();
```

At this point, **getApiKey()** will return null, **We need to implement it in native-lib.cpp**.

IDE will show an error on this line, because we have not implemented it yet.

Right click on the error and select **"Create JNI function for getApiKey()"**

**Step 6** : Now the code will look like this

```C++
#include <jni.h>
// Some Comments
extern "C"
JNIEXPORT jstring JNICALL
Java_com_ripalnakiya_apikeysndk_MainActivity_getApiKey(JNIEnv *env, jobject thiz) {
    // TODO: implement getApiKey()
}
```

**Step 7** : Now we need to return the API key from this function. So replace the comment with the following code

```C++
jstring API_KEY = (jstring) "MyAPIKeyInNDK";
return env->NewStringUTF(reinterpret_cast<const char *>(API_KEY));
```

**Step 8** : Now we can use this API key in our MainActivity.java file

```Java
String API_KEY = getApiKey();
```
