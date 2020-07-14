# Reactive Programming

## 1. Reactive Programming 이란

- 비동기적 데이티 흐름을 처리하는 프로그래밍이다.
- 모든 것을 `비동기적인 데이터의 Stream` 으로 간주하고, `Observer 디자인 패턴`을 활용해서 이러한 비동기 이벤트들을 처리한다.

### 1.1 Stream

- `시간 순`으로 정렬된 진행 중인 이벤트들의 흐름이다.
- 이 흐름은 `값, 에러, 또는 완료 신호(이벤트)`를 `발행(Emit)`한다.
- 이렇게 내보내진 이벤트들을 `비동기적`으로 감지할 것이며 이는 각각의 이벤트가 발행될 때 실행되는 함수를 정의함으로써 이루어진다.

### 1.2 Observer Design Pattern

- 흐름을 `"듣는"` 행위는 `구독 (Subscribing)` 이라고 불린다.
- 우리가 정의한 함수들은 `옵저버 (Observer)` 들이다.
- 그리고 그 흐름은 `Subject 또는 Observable` 로 불리는 `감지가 가능한 것`들이다.

### 1.3 Reactive Programming 을 사용하는 이유

- 코드의 추상화 수준을 높인다
- 실시간 이벤트들을 다루기 용이하다.
- 비동기적 이벤트를 마치 iterable 컬렉션처럼 다룰 수 있다.

</br>

## 2. RxJava, RxKotlin, RxAndroid

- **RxJava : Java(JVM)를 위한 ReactiveX Extensions**
- **RxKotlin : Kotlin을 위한 ReactiveX Extensions**
- **RxAndroid : Android을 위한 ReactiveX Extensions**

</br>

## 3. RxJava 시작하기

### 3.1 build.**gradle (app) 에 dependency 추가**

```kotlin
ext {
    RxJava = '3.0.0'
    RxAndroid = '3.0.0'
    RxKotlin = '3.0.0'
}

dependencies {
  // RxJava
  implementation "io.reactivex.rxjava3:rxandroid:$RxAndroid"
  implementation "io.reactivex.rxjava3:rxjava:$RxJava"
  implementation "io.reactivex.rxjava3:rxkotlin:$RxKotlin"

  implementation "com.github.akarnokd:rxjava3-retrofit-adapter:3.0.0"
}
```

### 3.2 build.gradle (project) 에 repositories 추가

```kotlin
allprojects {
    repositories {
        maven { url "https://oss.jfrog.org/libs-snapshot" }
    }
}
```

### 3.3 Retrofit 에 RxJavaCallAdapterFactory 추가

- Call<T> 대신 RxJava에서 사용되는 Observables 객체를 반환할 수 있도록 한다.

```kotlin
@Provides
fun provideRetrofit(baseURL: String, client: OkHttpClient): Retrofit {
    return Retrofit.Builder()
        .baseUrl(baseURL)
        .client(client)
        .addConverterFactory(GsonConverterFactory.create())
        .addCallAdapterFactory(RxJava3CallAdapterFactory.create())
        .build()
}
```

</br>

> **Observables**

1. Observable<T>
    - `Observable` 은 `옵저버 패턴을 구현`하여 객체의 상태 변화를 관찰하여 상태 변화가 있을 때마다 상태 변화에 대해 옵저버에게 알려준다.
2. Flowable<T>
    - 데이터가 발생하는 속도가 구독자가 처리하는 속도보다 현저하게 빠른 경우 발생하는 `배압 (Back Pressure) 이슈`에 대응하는 기능을 제공한다.
    - Observable 과의 차이는 `backpressure buffer 의 기본 탑재 유무`이다.
3. Single<T>
    - Observable 의 특수한 형태
    - Observable 은 데이터를 무한하게 발행할 수 있지만, `Single` 은 오직 `1개`의 데이터만 발행하도록 한정
    - 데이터 하나가 `발행(Emit)과 동시에 종료`
4. Maybe<T>
    - Single 과 마찬가지로 `최대 데이터 하나`를 가질 수 있지만 `데이터 발행 없이 바로 데이터 발생을 완료할 수 있다.` → 즉, 값을 보낼 수도 있고 안 보낼 수도 있다.
    - 값이 있는 경우엔 `onSuccess()`, 값이 없는 경우엔 `onComplete()` 가 호출
5. Completable
    - `Completable` 은 별도로 발생시키는 데이터 없이 `작업의 성공, 실패 여부`만 전파한다.

</br>

> **Back Pressure**

- 생산자는 미친듯이 `element 를 생산`해 내는데 소비자가 `처리하는 속도`가 이를 따라가지 못한다면
    1. `Busy Waiting` 또는
    2. `Out Of Memory Exception` 이 발생할 것이다.
- 흐름 제어를 위한 버퍼가 바로 `backpressure buffer` 이다.
- 버퍼가 가득 차면 어차피 소비자는 element 를 처리할 여유가 없는 상태이므로 `더 이상 publish 를 하지 않는다.`

### 3.4 API Interface 정의

- Observable 을 반환하는 Interface 함수

```kotlin
package com.knu.androidmvp.http

import com.knu.androidmvp.http.apimodel.GithubRepository
import io.reactivex.rxjava3.core.Observable
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Path

interface GithubAPI {

    @GET("users/{userId}/repos")
    fun getRepositoriesObservable(@Path("userId") userId: String): Observable<List<GithubRepository>>
}
```

### 3.5 예제

- `List<GithubRepository>` 타입의 데이터에 대해 `Observer 구현`
- 데이터가 `발행(Emit)` 될 때마다 정의한 함수를 수행

```kotlin
githubAPI.getRepositoriesObservable("qufwnfahs")
    .flatMap(object : Function<List<GithubRepository>, Observable<GithubRepository>> {
        override fun apply(t: List<GithubRepository>?): Observable<GithubRepository> {
            return Observable.fromIterable(t)
        }
    }).flatMap(object : Function<GithubRepository, Observable<String>> {
        override fun apply(t: GithubRepository?): Observable<String> {
            return Observable.just(t?.name)
        }
    }).filter(object : Predicate<String> {
        override fun test(t: String): Boolean {
            return t.startsWith("a")
        }
    })
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(object : Observer<String> {
        override fun onComplete() {
            println("rx java is complete")
        }
        override fun onSubscribe(d: Disposable?) {
            println("rx java onSubscribe $d")
        }
        override fun onNext(t: String?) {
            println("rx java : $t")
        }
        override fun onError(e: Throwable?) {
            e?.printStackTrace()
        }
})
```

- `subscribeOn(Scheduler scheduler)` : upstream `SingleSource`(또는 `MaybeSource`)의 `subscribe()`를 실행할 `Scheduler` 를 설정
- `observeOn(Scheduler scheduler)` : downstream `SingleObserver`(또는 `MaybeObserver`)의 동작(`onSuccess()`, `onError()`, `onComplete()`) 을 실행할 `Scheduler`를 설정
