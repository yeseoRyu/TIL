Enum Class
===

* 열거형 클래스
  - 가장 기본적인 사용 사례는 유형 안전 열거형을 구현하는 것

Kotlin의 Enum 클래스는 정의된 enum 상수를 나열하고   
이름으로 enum 상수를 가져오는 합성 속성과 메서드를 가지고 있다.   

```
    EnumClass.valueOf(value: String): EnumClass
    EnumClass.entries: EnumEntries<EnumClass> // specialized List<EnumClass>
```

개발시 하드코딩된 문자열이나 상수 사용을 최대한 줄이고,   
enum으로 관리하는 것이 좋다. 그 이유는 아래와 같다.

* 가독성 향상
  - 코드에서 0이나 1 대신 Position.HOME 같은 명확한 이름을 사용

* 유지보수 용이성
  - 새로운 위치가 추가될 때 Enum class에만 변경을 추가하면 됨

* 안정성 향상
  - 잘못된 index 값에 대한 처리가 내장됨

```
    enum class Position(val index: Int) {
        HOME(0),
        BOOKMARK(1);
        
        companion object {
            fun fromIndex(index: Int): Position {
                return entries.find { it.index == index }
                    ?: throw IllegalArgumentException("Unexpected position $index")
            }
        }
    }
```

***enum의 함수를 companion object 안에 선언하는 이유는?***

* 외부 클래스와 결합도 감소 
  - companion object를 사용하면 enum과 관련된 작업을 외부에서 따로 구현할 필요가 없다. 즉, Position에 관한 논리는 Position 내부에서 완전히 캡슐화됩니다.
  -  정적 함수와 유틸리티 메서드를 enum 클래스 내부에 정의하여, 코드의 응집성을 높이고 외부 의존성을 줄이는 데 유리하다.

<br>

+ 참고

코틀린 1.9.0에선 values()를 대체하는 entries 프로퍼티가 도입됐다.   
이것은 미리 할당된 enum 상수의 불변 리스트를 반환한다.   
이는 컬렉션으로 작업 시 유용하며 성능 문제를 방지하는 데 도움이 될 수 있다.