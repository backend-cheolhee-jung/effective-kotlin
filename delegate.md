## Kotlin Delegate

kotlin을 사용하다보면 delegate pattern을 사용해야 하는 경우가 있다.  
대표적으로 `lazy`나 `observable`, `vetoable`, `notNull` 등이 있다.  
그리고 delegate 뿐만 아니라 lateinit과 같은 키워드를 사용해서 변수를 초기화하지 않고 선언할 수 있다.  
이러한 delegate를 사용해서 property를 선언하는 것을 `delegated property`라고 하며 이 기법을 적극 활용하는 것이 좋다.

### lateinit

`lateinit`은 변수를 선언할 때 초기화를 하지 않고 선언할 수 있게 해주는 키워드이다.  
아래 예시는 비밀번호를 입력받는 경우와 같이 초기화가 필요하지만, 생성자에서 초기화할 수 없는 경우에 유용하다.

```kotlin
data class User(
    val email: String,
    val name: String,
) {
    lateinit var password: String
}

val user = User(
    name = "정철희",
    email = "ekxk1234@gmail.com",
)

user.password = PasswordEncryptor.encrypt("password1234")
```

하지만 lateinit은 Int, Double, Boolean과 같은 primitive 타입에는 사용할 수 없다.  
그래서 이러한 primitive 타입을 지연 초기화하고 싶다면 Delegate를 사용해야 한다.

### Delegate

Delegate는 property에 대해 특정 행위를 위임할 수 있게 해주는 기능이다.  
lateinit으로 해결할 수 없었던 primitive 타입의 지연 초기화를 NotNullDelegate를 사용해서 할 수 있다.  
그리고 lateinit은 var 변수에만 사용할 수 있지만, Delegate는 val 변수에도 사용할 수 있다.  
아래는 `notNull` Delegate를 사용한 예시이다.

```kotlin
class User(
    val email: String,
    val name: String,
) {
    lateinit var password: String
    val point by Delegate.notNull<Int>()
}
```

또 하나 observable을 예시로 보면 아래와 같이 property가 변경될 때마다 특정 행위를 수행할 수 있다.  
이렇게 Delegate를 사용하면 property가 변경될 때마다 특정 행위를 수행할 수 있다.

```kotlin
import kotlin.properties.Delegates

class User(
    val email: String,
    val name: String,
    token: String,
) {
    var token: String by Delegates.observable(token) { _, oldValue, newValue ->
        println("Token changed from $oldValue to $newValue")
    }
}
```

만약 위와 같이 값이 자주 변경 property가 많다면 custom delegate를 만들어서 사용할 수 있다.

```kotlin
class ChangeValueLogger<T>(private var value: T) {
    operator fun getValue(thisRef: Any?, property: kotlin.reflect.KProperty<*>): T {
        return value
    }

    operator fun setValue(thisRef: Any?, property: kotlin.reflect.KProperty<*>, newValue: T) {
        println("Property '${property.name}' changed from $value to $newValue")
        value = newValue
    }
}

class User(
    val email: String,
    val name: String,
    token: String,
) {
    var token by ChangeValueLogger(token)
}
```
