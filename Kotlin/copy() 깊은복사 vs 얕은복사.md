copy()
===

Kotlin에서 copy() 함수는 주로 data class에서 사용되며, 객체의 불변성(immutable)을 유지하면서 일부 속성만 변경된 새로운 객체를 생성하는 데 유용하게 쓰인다.

<br>

> data class는 자동으로 copy() 메서드를 제공하여 일부 속성만 변경하면서 새로운 객체를 만들 수 있다.
이를 통해 기존 객체를 직접 수정하지 않고 불변성을 보장할 수 있게 한다.


<br><br>

## deep copy  vs  shallow copy

```
    val newPhoto1 = singlePhoto.apply {
        isLike = true
    }

    val newPhoto2 = singlePhoto.copy(isLike = true)
```

<br>

* 얕은 복사
  * newPhoto1은 singlePhoto와 주소값이 동일한 객체
  * singlePhoto를 가리키고 있을 뿐 새로 주소값이 생성되는 것은 아니다.

<br>

* 깊은 복사
  * copy()를 수행하면 새로운 객체가 생성된다.
  * 깊은 복사는 객체의 모든 값을 복사하여 내용은 동일하지만 주소는 다르다.
  * 또한 원하는 값을 변경할 수도 있다.


<br><br>

### 자바의 생성자를 활용한 객체 복사

'''
    constructor(blurHash: String?): this(blurHash, color, createdAt, description, downloads, height, id, likedByUser, likes, publicDomain, updatedAt, width, exif, urls, user)
'''

* 자바의 경우에는 immutable하게 복사를 해야한다고 하면 위와 같이 복잡하고 중첩된 코드를 작성해야 한다. 그러나 이러한 것이 kotlin에서는 copy()로 간단하게 적용이 가능한 것이다.


<br><br>

### 메모리에 미치는 영향

* copy()를 호출하면 기존 객체를 기반으로 새로운 객체가 힙 메모리에 할당된다. 이는 메모리 사용량이 증가할 수 있음을 의미한다.
