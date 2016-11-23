Logging Interceptor
===================

An [OkHttp interceptor][1] which logs HTTP request and response data.

```java
HttpLoggingInterceptor logging = new HttpLoggingInterceptor();
logging.setLevel(Level.BASIC);
OkHttpClient client = new OkHttpClient.Builder()
  .addInterceptor(logging)
  .build();
```

You can change the log level at any time by calling `setLevel`.

You can also implements Interceptor by youself:
```java
public class OkHttpLogoInterceptor implements Interceptor {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();
        Log.e("okhttp", String.format("Sending request %s on %s%n%s", request.url(),
                chain.connection(), request.headers()));

        long t1 = System.nanoTime();
        Response response = chain.proceed(chain.request());
        long t2 = System.nanoTime();
        Log.e("okhttp", String.format(Locale.getDefault(), "Received response for %s in %.1f ms %n%s",
                response.request().url(), (t2 - t1) /1e6d, response.headers()));
        okhttp3.MediaType mediaType = response.body().contentType();
        String content = response.body().string();
        Log.e("okhttp", content);
        return response.newBuilder()
                .body((okhttp3.ResponseBody.create(mediaType, content)))
                .build();
    }
}
```

To log to a custom location, pass a `Logger` instance to the constructor.
```java
HttpLoggingInterceptor logging = new HttpLoggingInterceptor(new Logger() {
  @Override public void log(String message) {
    Timber.tag("OkHttp").d(message);
  }
});
```

**Warning**: The logs generated by this interceptor when using the `HEADERS` or `BODY` levels has
the potential to leak sensitive information such as "Authorization" or "Cookie" headers and the
contents of request and response bodies. This data should only be logged in a controlled way or in
a non-production environment.


Download
--------

Get via Maven:
```xml
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>logging-interceptor</artifactId>
  <version>(insert latest version)</version>
</dependency>
```

or via Gradle 
```groovy
compile 'com.squareup.okhttp3:logging-interceptor:(insert latest version)'
```

Some contents copy from: https://github.com/square/okhttp/edit/master/okhttp-logging-interceptor/README.md, 
thanks for the auther.

 [1]: https://github.com/square/okhttp/wiki/Interceptors

