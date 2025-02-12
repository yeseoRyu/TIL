## 동시성을 보장하는 컬렉션

동시성이 보장되는 컬렉션은 대표적으로 `Vector`와 `HashTable`이 있다.

<br>

> 그렇다면 이 컬렉션은 자주 쓰이는가? 결론은 X

* 이 자료구조는 synchronized 블럭이 감싸져 있다. <br>
 이에 동시성이 보장되어 데이터 안정성을 보장한다는 장점이 있지만, 이 과정에서 **락(Lock)**을 걸기 때문에 단일 스레드 환경에서는 불필요한 리소스 낭비가 있을 수 있다. <br>
  따라서 성능과 속도가 빠른 `ArrayList`나 `HashMap` 등을 더 자주 사용한다.

```
public class Vector<E> extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    ...
    public synchronized boolean add(E e) {
                add(e, elementData, elementCount);
                return true;
    }
    ...
}
```
