# Error Handling
<show-structure depth="2"/>

```kotlin
sealed class Response {
    class Loading: Response()
    class Success(val postList: List<Post>): Response()
    class Failure(val errorMessage: String): Response()
}
```

```kotlin
sealed class Response(val postList: List<Post>? = null, val errorMessage: String? = null) {
    class Loading : Response()
    class Success(postList: List<Post>?) : Response(postList = postList)
    class Failure(errorMessage: String?) : Response(errorMessage = errorMessage)
}
```

This version is better than previous one, but this `Response` class can only be used with one API...

To make it useful for other APIs as well, use generics:

```kotlin
sealed class Response<T>(val data: T? = null, val message: String? = null) {
    class Loading<T> : Response<T>()
    class Success<T>(data: T?) : Response<T>(data = data)
    class Failure<T>(message: String?) : Response<T>(message = message)
}
```

## Example

In this example, **Repostory** controls **Response** object.

```kotlin
class PostRepository(private val postDao: PostDao, private val postApi: PostApi) {
    private val _list = MutableLiveData<Response<List<Post>>>()

    val list: LiveData<Response<List<Post>>>
        get() = _list

    suspend fun fetchAll() {
        _list.postValue(Response.Loading())
        if (NetworkUtil.isNetworkAvailable) {
            val result = postApi.getAll()
            if (result.isSuccessful && result.body() != null) {
                val posts = result.body()!!
                postDao.addAll(posts)
                _list.postValue(Response.Success(posts.toList()))
            } else {
                _list.postValue(Response.Failure("Error in Contacting Server"))
            }
        } else {
            _list.postValue(Response.Success(postDao.getAll()))
        }
    }
}
```

Updates from **Repository** reach to **ViewModel**:

```kotlin
class MainViewModel(private val postRepository: PostRepository): ViewModel() {

    val posts: LiveData<Response<List<Post>>>
        get() = postRepository.list

    init {
        viewModelScope.launch {
            postRepository.fetchAll()
        }
    }
}
```

And from **ViewModel** to **Activity**:

```kotlin
viewModel.posts.observe(this) {
    when (it) {
        is Response.Loading -> {
            Log.d(TAG, "loading")
        }
        is Response.Success -> {
            it.data?.let { posts ->
                for (post in posts) { Log.d(TAG, "post: $post") }
            }
        }
        is Response.Failure -> {
            Log.d(TAG, "error: ${it.message}")
        }
    }
}
```
