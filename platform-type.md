## platform type, stated type, inferred type

### 개념

플랫폼 타입이란 null이 될 수도 있고, null이 아닐 수도 있는 타입이다.(타입 힌팅 표기가 될 때 `!`가 붙는다.)  
스테이티드 타입은 코드에 명시적으로 타입을 표기한 타입이고, 인퍼드 타입은 컴파일러가 추론한 타입이다.

### 플랫폼 타입을 사용하지 말아야 하는 이유

나는 코틀린 컴파일러와 Intellij가 제공하는 타입 힌팅 사용을 지향해왔다.  
설정과 관련된 부분들은 Java로 구현된 코드가 많기 때문에 플랫폼 타입을 사용해야 하는 경우가 많았다.  
그런데 나는 플랫폼 별도로 처리하지 않을 때가 종종 있었는데, 이 책을 읽고 플랫폼 타입을 사용하지 말아야겠다고 생각했다.

아래는 platform type과 stated type을 비교한 예시이다.

```java
public class JavaClass {
    public String getName() {
        return null;
    }
}
```

```kotlin
fun platformType(javaClass: JavaClass) {
    val name = javaClass.name // 표기될 때는 `String!`로 표기됨
    println(name.length) // 이 시점에 NPE가 발생
}

fun statedType(javaClass: JavaClass) {
    val name: String = javaClass.name // 이 시점에 NPE가 발생
    println(name.length)
}
```

지금처럼 platform 타입을 사용하면, 값이 사용되는 시점에 NPE가 발생하기 때문에 디버깅을 할 때 어디서 문제가 발생했는지 찾기 어렵다.  
반대로 스테이티드 타입을 사용하면, 값이 사용되기 전에 NPE가 발생하기 때문에 디버깅을 할 때 어디서 문제가 발생했는지 찾기 쉽다.

그리고 infered type을 자주 사용하게 된다면 intellij 환경이 아닌 다른 IDE를 사용하게 될 때, 타입 힌팅이 제대로 되지 않아 불편할 수 있어서 스테이티드 타입을 사용하는 것이 좋은 것 같다.  

