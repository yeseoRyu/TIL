Inner Calss와 Nested Class의 차이

코틀린에서 inner class와 nested class는 모두 클래스 안에 정의된 클래스이지만,   
***외부 클래스와의 관계와 사용 방식***에서 차이가 있습니다.


## Nested class (중첩 클래스)

* 기본적으로 static으로 동작합니다.
* 외부 클래스의 인스턴스에 접근할 수 없습니다.

<br>

## inner class (내부 클래스)

* 외부 클래스의 인스턴스와 연결되어 동작합니다.
* 외부 클래스의 멤버 변수나 메서드에 직접 접근할 수 있습니다.


---

* 선택 방식
  - 외부 클래스와 독립적으로 동작해야 하는 경우: nested class
  - 외부 클래스와의 관계가 필요한 경우 (외부 멤버 사용 등): inner class


#### RecyclerView Adapter 의 ViewHolder 를 정의할 때
ViewHolder는 각각의 Adapter 에서 독립적으로 사용되는 객체이며,
다른 Adapter와의 참조로부터 안전해야 한다. 따라서 Nested class 로 선언하자!