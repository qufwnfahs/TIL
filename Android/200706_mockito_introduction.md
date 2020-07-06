# Mockito

### 1. Mokito란 ?

유닛 테스트를 위한 Java Mocking Framework

단위 테스트를 하기 위해 `가짜 객체 (Mock)` 를 만들어주는 프레임워크이다.

> Mock 을 사용하는 이유

Business Logic의 Result 가 아닌 `Behavior` 를 `Test` 하고자 할 때 사용한다.

</br>

예를 들어, 데이터베이스에 Book 이 있다면, 데이터베이스에서 정보를 가져오고, 없다면 Web Service에서 Book의 정보를 가져오도록 설계했다고 한다면

위 상황을 `Test Code`를 통해 데이터베이스에 Book 정보가 있을 때에는 Web Service를 호출하지 않고 , 데이터베이스에 Book 정보가 없을 때만 Web Service를 호출하는 지를 `Test` 해야 한다.

</br>

이때 우리는 **'제대로된 결과값을 해당 메소드가 return'** 하는가? 가 아닌, 특정 상황에서 **'의도한 대로 business logic이 행동'**하는가? 를 검증해야 한다.

→ **행위 검증 (Behavior Verification)**

</br>

**제대로된 결과값을 return 하는가는 Assert로 검증할 수 있다.**

</br>

### 2. Mokito 시작하기

Mokito는 JUnit 위에서 동작하며 Mocking 과 Verification 을 도와주는 프레임워크이다.

**build gradle (app) 에 dependency 추가**

```kotlin
dependencies {
	...
	testImplementation group: 'org.mockito', name: 'mockito-all', version: '1.9.5'
	...
}
```

</br>

### 3. Mock 객체 생성

```kotlin
mockLoginModel = mock(LoginActivityMVPModel::class.java)
```

mock으로 생성한 객체는 `dummy 객체`로 실제 내부에 구현된 메소드는 수행되지 않고, 특정 상황에서 특정 메소드나 business logic이 수행되는지만 체크한다.

</br>

따라서 Mock 객체를 제대로 사용하기 위해서는 아래와 같이 특정 메소드의 return 값을 명시해줘야 한다.

이를 통해 Mock으로 생성한 객체의 메소드는 하드코딩한 결과값을 return 하게 된다.

```kotlin
// when -> thenReturn
`when`(mockLoginModel.getUser)).thenReturn(user)
```

</br>

### 4. Mokito 검증하기

`verity()` 메소드를 통해 메소드가 호출되는 지 검증할 수 있다.

</br>

**작성한 테스트 코드**

```kotlin
class PresenterTest {

    // @before : 테스트 실행 시마다, 제일 먼저 실행
    lateinit var mockLoginModel: LoginActivityMVP.Model
    lateinit var mockView: LoginActivityMVP.View
    lateinit var presenter: LoginActivityMVP.Presenter
    lateinit var user: User

    @Before // not effected by previous tests
    fun setUp() {
        // mockito set up, which we don't know anything about.
        mockLoginModel = mock(LoginActivityMVP.Model::class.java)

        user = User("Fox", "Mulder")

        mockView = mock(LoginActivityMVP.View::class.java)

        presenter = LoginActivityPresenter(mockLoginModel)

        presenter.setView(mockView)
    }

		@Test
    fun shouldCreateErrorMessageIfFeildAreEmpty() {
        `when`(mockView.getFirstName()).thenReturn("")  // empty string

        presenter.saveUser()

        // verify view interactions
        verify(mockView, times(1)).getFirstName()
        verify(mockView, never()).getLastName()
        verify(mockView, times(1)).showInputError()

        `when`(mockView.getFirstName()).thenReturn("Dana")
        `when`(mockView.getLastName()).thenReturn("") // empty string

        presenter.saveUser()

        // 내부적으로 Mockito는 invocation Count를 계속 추적한다.
        // 즉 total call tiems를 적어줘야 한다.
        verify(mockView, times(2)).getFirstName()
        verify(mockView, times(1)).getLastName()
        verify(mockView, times(2)).showInputError()
    }
}

// 파라미터 값이 달라져도 Fail (Fox -> Dana)
// verify(mockView, times(1)).setFirstName("Fox") 
```

> @Before

테스트 케이스 시작 전 매번 호출되는 메소드 명시

> @Test

테스트할 메소드 명시