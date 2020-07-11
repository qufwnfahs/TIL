# Retrofit Introduction

### 1. Retrofit 소개

Retrofit은 **Type-Safe** 한 **HttpClient 라이브러리**이다.

`Type-Safe` 은 네트워크로부터 전달된 데이터를 우리 프로그램에서 `필요한 형태의 객체` 로 받을 수 있다는 의미이다.

Retrofit은 기본적으로 **OKHttp**에 의존하고 있다.

Retrofit은 `데이터 직렬화 과정`을 위한 `Converter`를 구성할 수 있으며, `JSON (또는 다른 구조적인 데이터)`을 `REST 기반 API`를 통해 주고 받기를 상대적으로 쉽게 해준다.

</br>

### 2. Retrofit 시작하기

[https://square.github.io/retrofit/](https://square.github.io/retrofit/)

### 2.1 build.**gradle (app) 에 dependency 추가**

```kotlin
ext {
    retrofit = '2.9.0'
    gson = '2.8.6'
    okhttp3_logging_interceptor = '4.7.2'
}

dependencies {
    ...
    // REST Interactions
    implementation "com.squareup.retrofit2:retrofit:$retrofit"

    // JSON Parsing
    implementation "com.squareup.retrofit2:converter-gson:$retrofit"
    implementation "com.google.code.gson:gson:$gson"

    // HTTP Logging
    implementation "com.squareup.okhttp3:logging-interceptor:$okhttp3_logging_interceptor"
    ...
}
```

### 2.2 예제 JSON

[https://api.github.com/users/qufwnfahs/repos](https://api.github.com/users/qufwnfahs/repos)

Github의 저장소 API를 사용

### 2.3 JSON TO Kotlin

`JSON`을 바탕으로 `Kotlin 모델 클래스` 작성

[https://www.json2kotlin.com/](https://www.json2kotlin.com/)

위 사이트를 이용하여 변환을 쉽게 할 수 있다.

```kotlin
import com.google.gson.annotations.SerializedName

data class GithubRepository (

    @SerializedName("id") val id : Int,
    @SerializedName("node_id") val node_id : String,
    @SerializedName("name") val name : String,
    ...
}
```

### 2.4 Interface 에 HTTP API 기술

Retrofit은 `Interface`에 기술된 명세를 `HTTP API`로 전환해 준다. 

따라서, 우리가 요청할 API들에 대한 명세들을 Interface에 기술해야 한다.

```kotlin
import com.knu.androidmvp.http.apimodel.GithubRepository
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Path

interface GithubAPI {

    @GET("users/{userId}/repos")
    fun getRepositories(@Path("userId") userId: String): Call<List<GithubRepository>>
}
```
</br>

> URL 다루기

요청 URL은 동적으로 부분 치환이 가능하며, 이는 메소드의 파라미터로 변경이 가능하다.

부분 치환은 영문 / 숫자로 이루어진 문자열을 **'{' 와 '}'**로 감싸 정의해준다.

반드시 이에 대응하는 `@Path` 를 메소드 파라미터로 명시해줘야 한다.

</br>

### 1) @Path

```kotlin
@GET("users/{userId}/repos")
fun getRepositories(@Path("userId") userId: String): Call<List<GithubRepository>>
```

URL Path가 동적으로 들어가야 할 때 `@Path` 사용

**GET https://api.github.com/users/qufwnfahs/repos**

</br>

### 2) @Query

```kotlin
@GET("kraken/search/streams")
fun searchChannels(@Query("query") query: String): Call<ChannelData>
```

URL에 Query 파라미터를 넣어줄 경우에 사용한다. 

`@Query` 는 GET 방식에서만 사용 가능하다.

**GET [https://api.twitch.tv/kraken/search/streams?](https://api.twitch.tv/kraken/search/streams?=)query=LOL**

</br>

### 2-1) @QueryMap

```kotlin
@GET("/group/{id}/users")
fun groupList(@Path("id") groupId: Int, @QueryMap options: Map<String, String>): Call<List<User>>
```

보다 동적이며 다양한 Query 파라미터들은 **Map**으로도 사용할 수 있다.

</br>

### 3) @Body

```kotlin
@POST("/users/new")
fun createUser(@Body user: User): Call<User>
```

객체를 `@Body` 어노테이션을 통해 명시가 가능하다.

이 객체들은 Retrofit 인스턴스에 추가된 **Converter**에 따라 변환된다.

</br>

### 4) @Field

```kotlin
@FormUrlEncoded
@POST("/user/edit")
fun updateUser(@Field("first_name") first: String, @Field("last_name") last: String): Call<User>
```

`@FormUrlEncoded` 어노테이션을 메소드에 명시하면 **form-encoded** 데이터로 전송 된다.

각 `@Field key-value pair`의 `key`는 어노테이션 값에, `value`는 파라미터로 명시하면 된다.

</br>

### 4-1) @FieldMap

```kotlin
@FormUrlEncoded 
@POST("/posts") 
fun postFirst(@FieldMap parameters: HashMap<String, Object>): Call<ResponseGet>
```
</br>

### 2.5 Retofit 설정

Retrofit 구성에 필요한 인스턴스들을 모두 정의한 `Module 클래스` 생성하여 `Component`에게 `Inject` 가능하게 만들어준다.

### 2.5.1 HttpLoggingInterceptor 추가

```kotlin
@Provides
fun provideClient(): OkHttpClient {
    val interceptor = HttpLoggingInterceptor()
    interceptor.level = HttpLoggingInterceptor.Level.BODY

    return OkHttpClient.Builder().addInterceptor(interceptor).build()
}
```

### 2.5.2 Retrofit 인스턴스

```kotlin
@Provides
fun provideRetrofit(baseURL: String, client: OkHttpClient): Retrofit {
    return Retrofit.Builder()
	.baseUrl(baseURL)
	.client(client)
	.addConverterFactory(GsonConverterFactory.create())
	.build()
}
```

- `baseUrl()` : 통신 종단점 URL 설정
- `addConverterFactory()` : Converter 추가
</br>

**<ApiModule.class>**

```kotlin
package com.knu.androidmvp.http

import dagger.Module
import dagger.Provides
import okhttp3.OkHttpClient
import okhttp3.logging.HttpLoggingInterceptor
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

@Module
class ApiModule {

    // HttpLoggingInterceptor를 사용하기 위해 만든 함수
    @Provides
    fun provideClient(): OkHttpClient {
        val interceptor = HttpLoggingInterceptor()
        interceptor.level = HttpLoggingInterceptor.Level.BODY

        return OkHttpClient.Builder().addInterceptor(interceptor).build()
    }

    @Provides
    fun provideRetrofit(baseURL: String, client: OkHttpClient): Retrofit {
        return Retrofit.Builder()
            .baseUrl(baseURL)
            .client(client)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    @Provides
    fun provideApiService(): GithubAPI {
        return provideRetrofit(BASE_URL, provideClient()).create(GithubAPI::class.java)
    }

    companion object {
        const val BASE_URL = "https://api.github.com/"
    }
}
```
</br>

### 2.6 Retrofit 사용하기

```kotlin
// API가 명세된 Interface
@Inject
lateinit var githubAPI: GithubAPI

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_login)
		
    (application as App).getComponent().inject(this)	// Module Inject to Component

    // Retrofit
    val call = githubAPI.getRepositories("qufwnfahs")
    call.enqueue(object : Callback<List<GithubRepository>> {
        override fun onResponse(call: Call<List<GithubRepository>>, response: Response<List<GithubRepository>>) {
            // onResponse는 무조건 불린다.
            // response가 올바르지 않다면 response.body() 는 null 을 return
            for (repository in response.body() as List<GithubRepository>) {
                println(repository.name)
            }
        }
        override fun onFailure(call: Call<List<GithubRepository>>, t: Throwable) {
            t.printStackTrace()
        }
    })
}
```
</br>

### 3. 동기 VS. 비동기

`Call` 인스턴스는 `동기 혹은 비동기`로 요청 실행이 가능하다.

각 인스턴스들은 동기 혹은 비동기 중 한 가지 방식만 사용이 가능하다. 하지만 `clone()` 메소드를 통해 새 인스턴스를 생성하면 이전과 다른 방식을 사용 가능하다.

</br>

안드로이드에서 `Callback` 들은 `Main Thread`에서 실행된다.

JVM에서는 **HTTP을 요청을 호출한 Thread와 동일한 Thread**에서 `Callback` 들이 실행된다.
