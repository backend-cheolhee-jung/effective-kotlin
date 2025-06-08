## 불변을 다루는 방법

kotlin에서는 유지보수성, 디버깅을 위해서 불변을 다루는 방법을 제공한다.  
일반적으로 val, var 키워드를 사용해서 변수를 선언하고는 한다.  
이를 조금 더 활용해서 가변 배열과 불변 배열을 효율적으로 사용하는 방법을 정리해보려한다.  

일반적인 객체에서는 가변 배열을 선언하고 사용하는 경우가 많다.  
하지만 가변 배열을 사용하게 되면, 비즈니스 로직에서 배열이 변경되고 파악하기 어려운 경우가 많다.  
이러한 문제를 해결하려면 외부에 노출되는 API에서는 불변 배열을 사용하고, 내부에서는 가변 배열을 사용하는 것이 좋다.  
이렇게 하면 외부에서 배열을 변경할 수 없고, 특정 함수를 사용해서 배열을 변경할 수 있기 때문에 비즈니스 로직에서 배열이 변경되는 것을 파악하기 쉬워진다.  

아래는 가변 배열과 불변 배열을 사용하는 예시이다.  

```kotlin
class User(
    val email: String,
    val name: String,
    private val _books: MutableList<String> = mutableListOf(), 
) {
    var books: List<String> // 불변 배열이지만 var로 선언해서 재할당이 가능하도록 수정.
        private set // 외부에서 books를 변경할 수 없도록 private set으로 설정
    
    init {
        books = _books.toList() // 초기화 시점에 가변 배열을 불변 배열로 변환
    }
    
    fun addBook(book: String) {
        _books.add(book) // 가변 배열에 책 추가
        books = _books.toList() // 불변 배열로 업데이트
    }
}
```