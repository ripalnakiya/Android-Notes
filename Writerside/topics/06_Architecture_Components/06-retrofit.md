# Retrofit
<show-structure depth="2"/>

[Refer Retrofit docs](https://lysine.dev/retrofit/)

## Add dependencies

```text
[versions]
// ...
retrofit = "3.1.0"

[libraries]
// ...
retrofit = { module = "com.squareup.retrofit2:retrofit", version.ref = "retrofit" }
converter-gson = { module = "com.squareup.retrofit2:converter-gson", version.ref = "retrofit" }
```

```kotlin
dependencies {
    // ...
    implementation(libs.retrofit)
    implementation(libs.converter.gson)
}
```

## Define DTOs

> Data Transfer Object: just a Kotlin class whose job is to represent data coming from or going to an API

```kotlin
data class UserDto(
    val address: Address,
    val company: Company,
    val email: String,
    val id: Int,
    val name: String,
    val phone: String,
    val username: String,
    val website: String
)
```

> use **JsonToKotlinClass** plugin in android studio

## Define interface

```kotlin
interface Api {

    @GET("users")
    suspend fun getUsers(): Response<ArrayList<UserDto>>
}
```

> Retrofit will generate the implementation of this interface.

## Build Retrofit Instance

```kotlin
object RetrofitUtil {
    private const val BASE_URL = "https://jsonplaceholder.typicode.com/"

    fun getInstance(): Retrofit {
        return Retrofit.Builder()
            .baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
}
```

## Create Retrofit Instance for the interface

```kotlin
val api = RetrofitUtil.getInstance().create(Api::class.java)

// use the retrofit intance
GlobalScope.launch {
    val result = api.getUsers()
    val usersList = result.body()
    if (usersList != null) {
        for (user in usersList) {
            Log.d(TAG, "user: $user")
        }
    }
}
```
