# Retrofit

**Retrofit** is a popular **open-source library** that simplifies the process of **making HTTP requests** and **handling API responses**. It is commonly used to interact with RESTful APIs.

**Gson** is a Java library for converting **Java objects to JSON format** and vice versa.

**Picasso** is a popular **open-source image loading library** for Android. It simplifies the process of **loading and displaying images** in Android applications.

**Logging Interceptor** is an interceptor used to **log information about outgoing HTTP requests and incoming HTTP responses**. It's a powerful tool for **debugging and analyzing network interactions** in your application

## Implementation

MainActivity.java

```java
Gson gson = new GsonBuilder().serializeNulls().create();

Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("https://jsonplaceholder.typicode.com/")
        .addConverterFactory(GsonConverterFactory.create(gson))
        .build();

private JsonPlaceholderAPI jsonPlaceholderAPI = jsonPlaceholderAPI = retrofit.create(JsonPlaceholderAPI.class);

// Making a Get request

Call<List<Post>> call = jsonPlaceholderAPI.getPosts();

call.enqueue(new Callback<List<Post>>() {
    @Override
    public void onResponse(Call<List<Post>> call, Response<List<Post>> response) {

        if (!response.isSuccessful()) {
        Log.d(TAG, "onResponse: " + response.code());
            return;
        }

        List<Post> posts = response.body();
        showPosts(posts);
    }

    @Override
    public void onFailure(Call<List<Post>> call, Throwable t) {
        Log.d(TAG, "onResponse: " + t.getMessage());
    }
});

// Making a Post request

Post post = new Post(23, "New title", "New text");
Call<Post> call = jsonPlaceholderAPI.createPost(post);

call.enqueue(new Callback<Post>() {
    @Override
    public void onResponse(Call<Post> call, Response<Post> response) {

        if (!response.isSuccessful()) {
        Log.d(TAG, "onResponse: " + response.code());
            return;
        }

        Post postRespose = response.body();
        showPost(postRespose);
    }

    @Override
    public void onFailure(Call<Post> call, Throwable t) {
        Log.d(TAG, "onResponse: " + t.getMessage());
    }
});
```

JsonPlaceholderAPI.java

```java
public interface JsonPlaceholderAPI {

    @GET("posts")
    Call<List<Post>> getPosts();

    @POST("posts")
    Call<Post> createPost(@Body Post post);
}
```
