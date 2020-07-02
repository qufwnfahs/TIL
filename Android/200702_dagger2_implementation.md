## 1. Dagger 2 시작하기

------

**build gradle (app) 에 dependency 추가**

- BuffterKnife 10.0.0 이전 버전은 AndroidX에 대해 지원을 하지 않는다.

```scheme
apply plugin: 'kotlin-kapt'

android { 
	...
	/**
	 * generateStubs : Component 참조를 Kotlin 파일에 기술하기 위한 옵션
	 */
	kapt {
			generateStubs = true
  }

	/**
		* ButterKnife requires Java 8
		*/ 
	compileOptions {
			sourceCompatibility JavaVersion.VERSION_1_8
			targetCompatibility JavaVersion.VERSION_1_8
	}
	...
}
dependencies {
	...
	implementation "com.google.dagger:dagger:$dagger2_version"
	kapt "com.google.dagger:dagger-compiler:$dagger2_version"
	implementation 'com.jakewharton:butterknife:10.0.0'
	kapt 'com.jakewharton:butterknife-compiler:10.0.0'
	...
}
```

> AndroidX

- 기존에 사용 중인 com.android.support.* 라이브러리들을 크게 개선하여 하나로 통합한 것

> kotlin-kapt

- Kotlin Annotation Processing
- **일반 javac보다 먼저 kotlinc가 동작한다.**



### 1.1 Overriding Application

Application 클래스를 Override할 경우, 가장 먼저 객체화시키기 위해 AndroidManifest.xml의 Application 속성에 **android:name** 을 지정





## 2. 의존성 주입 (Dependency Injection)

------

의존성 주입의 개념을 명확히 하자.



### 2.1 Dependency Non-Injection

```java
public class Example {
	private DatabaseThingie myDatabase;

	public Example() {
		myDatabase = new DatabaseThingie();
	}

	public void DoStuff() {
		// ...
		myDatabase.getData();
		// ...
	}
}
```

**myDatabase** 라는 클래스 변수가 있고, 생성자에서 초기화되어 인스턴스 변수가 된다.

그리고 인스턴스 변수를 통해 메소드를 호출한다.

- 이 모든 것을 **"의존성 (dependency)"** 이라 부를 수 있다.



### 2.2 Dependency Injection

```java
public class Example {
	private DatabaseThingie myDatabase;

	public Example() {
		myDatabase = new DatabaseThingie();
	}

	public Example(DatabaseThingie useThisDatabaseInstead) {
		myDatabase = useThisDatabaseInstead;
	}

	public void DoStuff() {
		// ...
		myDatabase.getData();
		// ...
	}
}
```

변수를 생성자에 전달할 수 있다.

이를 통해 클래스에 **"의존성 (dependency)"** 을 **"주입 (inject)"** 한다.



이것이 전부다.  나머지는 이 기본 패턴의 변형들이다.

1. Constructor Injection (생성자 주입)
   - 필요한 의존성을 모두 포함하는 클래스의 생성자를 만들고 그 생성자를 통해 의존성을 주입
2. Method(Setter) Injection (Setter를 통한 주입)
   - 의존성을 입력받는 세터(Setter) 메소드를 만들고 이를 통해 의존성을 주입
3. Interface Injection (인터페이스를 통한 주입)
   - 의존성을 주입하는 함수를 포함한 인터페이스를 작성하고 이 인터페이스를 구현하도록 함으로써 실행 시에 이를 통하여 의존성을 주입



즉, 의존성 주입이란 **구성**과 **사용**의 책임을 구분하는 것이다.

**의존성 주입을 통해 의존성이 분리하게 되고, 그로 인해 기존의 의존 관계가 역전되게 된다.**





## 3. Dagger 기본 개념 이해하기

Dagger는 다음 5가지의 필수 개념이 있다.

1. Inject
2. Component
3. Subcomponent
4. Module
5. Scope



### 3.1 Inject

- javax.inject.inject.annotation 을 이용하면 Dagger에 의존성을 요청하기 때문에, 명시적으로 의존성을 주입시킬 필요가 없다.
- @Inject annotation을 통해 의존성을 생성자에 선언(Declaring)하면, 아래와 같이 Dagger를 통해 의존성을 주입하게 된다.

```kotlin
import javax.inject.Inject

class MainActivity : AppCompatActivity(), MainActivityMVP.View {
	/**
		* 1. @Inject annotation 선언
		* 2. MainActivityMVP.Presenter 인스턴스 요청
		* 3. Dagger가 생성자 호출
		*/
	@Inject
	lateinit var presenter: MainActivityMVP.Presenter

	override fun onCreate(savedInstanceState: Bundle?) {
			//...
			(application as App).getComponent().inject(this)   // 런타임 시점에 inject
			//...
	}
}
```

생성자 외에도 메소드와 필드에도 @Inject annotation을 선언할 수 있다.



**Dagger**가 @Inject annotation 을 통해 의존성을 주입하기 위해서는 **아래 두 가지 annotation을 이용해야 한다.**

> @Provides

- 어떻게 의존성을 구성하고 제공하는지 정의하는 메소드에 이용한다.

> @Module

- 의존성을 제공하는 @Provides 메소드를 가진 클래스에 이용한다.
- 모든 @Provides 메소드는 @Module 클래스 안에 속해야 한다.

```kotlin
import dagger.Module
import dagger.Provides

@Module
class MainModule {
		/**
			* @Inject annotation 으로 요청하는 인스턴스들을 Dagger는
			* @Provieds annotation 으로 정의된 메소드, 생성자 또는 필드로부터 가져온다.
			*/
    @Provides
    fun provideMainPresenter(model: MainActivityMVP.Model): MainActivityMVP.Presenter {
        return MainActivityPresenter(model)
    }

    @Provides
    fun provideMainModel(): MainActivityMVP.Model {
        return MainModel()
    }
}
```



**@Inject와 @Module 사이의 의존성을 연결하기 위해서는** dagger.Component.annotation을 이용해야 한다.

> @Component

- Interface 에만 이용할 수 있으며 Component 를 구성하는 모든 @Module 클래스 목록을 작성해야 한다.

```kotlin
import dagger.Component

@Component(modules = [MainModule::class])
interface ApplicationComponent {
    fun inject(target: MainActivity) 
}
```

@Component interface 까지 만들면, Dagger는 이 interface를 implement 한 클래스를 자동으로 생성합니다.

생성된 클래스는 @Component interface 이름 앞에 **"Dagger"**를 붙이게 된다.



Dagger를 이용해 의존성 주입을 구현하고, 해당 인스턴스를 얻어오기 위해 **build()와 create()**, 두 가지 방법을 이용할 수 있다.

> build()

1. Dagger에 의해 생성된 DaggerApplicationComponent 클래스의 builder() 메소드를 호출
2. @Component를 구성하는 Module 인스턴스(MainModule()) 설정
3. build() 메소드 호출
4. 필요할 때 getComponent()로 ApplicationComponent 참조 (ex: Activity의 onCreate())

```kotlin
class App: Application() {
    private lateinit var component: ApplicationComponent

    override fun onCreate() {
        super.onCreate()
				// build
        component = DaggerApplicationComponent.builder()
            .mainModule(MainModule())
            .build()
    }

    fun getComponent(): ApplicationComponent { return component }
}
```

> create()

다른 Component와의 의존성이 없고, arg가 없는 default 생성자로 Module을 생성할 수 있다면 create() 메소드를 이용할 수 있다.

1. Dagger에 의해 생성된 DaggerMediaComponent 클래스의 create() 호출
2. MediaController 인스턴스 가져오기

```java
// create()
MediaComponent component = DaggerMediaComponent.create();
MediaController controller = component.mediaController();
```





## 참고

https://brunch.co.kr/@oemilk/71

위 글을 참고하여 작성하였다.