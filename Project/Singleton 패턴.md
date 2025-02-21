# Singleton 패턴

디자인 패턴 중 하나인 싱글톤은 매우 중요하기 때문에, 다른 디자인 패턴들도 많지만 이것 하나만이라도 반드시 제대로 이해하고 있어야 한다!

<br><br>

```
companion object {
    val UIITEMS_DB = "uiItems.db"

    @Volatile
    private var INSTANCE: AppDatabase? = null

    fun getInstance(context: Context): AppDatabase =
        INSTANCE ?: synchronized(this) {
            INSTANCE
                ?: buildDatabase(context).also { INSTANCE = it }
        }

    private fun buildDatabase(context: Context) =
        Room.databaseBuilder(context.applicationContext, AppDatabase::class.java, UIITEMS_DB)
            .build()
}
```

<br><br>

## synchronized

* Instance를 생성하는 코드가 `synchronized`로 감싸져 있는 이유는 DataBase 객체를 **싱글톤**으로 관리하기 위해서이다. 싱글톤으로 관리하면 어떤 장점이 있어서 그런걸까? `synchronized`를 사용하는 이유가 될 수도 있는데, 이는 **동시성** 문제를 해결하기 위함이다. 여러 스레드가 동시에 접근하는 것을 방지하기 위해 **락**을 걸어 처리하므로 대표적인 동시성 해결의 방안으로 사용한다.

* DCL (Double-Checked Locking)

  - 멀티스레드 환경에서 Singleton 패턴을 구현할 때 성능을 최적화하기 위해 사용
  - 반드시 `Volatile`와 함께 사용

<br>

## Volatile

* 동기화 키워드

  - 변수를 CPU 캐시가 아닌 메인 메모리(RAM)에 직접 저장하고 읽도록 보장하여, 여러 스레드가 동시에 접근할 때 데이터의 일관성을 유지할 수 있도록 한다.

<br><br>

## companion object vs object class

싱글톤 객체를 구현할 때, 두가지 모두 활용되는데 어떤 차이점이 있을까?

* companion object

  - `static` 키워드와 같다. 즉 **정적 멤버**로 인스턴스를 생성하지 않아도 어디서든 참조가 가능하다.
  - 최초로 접근할 때 초기화 되고, 참조가 없으면 GC에 의해 정리되므로 **메모리 효율적**이다.

* object class

  - 싱글톤 패턴을 쉽게 구현하도록 제공하는 키워드로, 프로그램 내에서 하나의 인스턴스를 가진다.
  - `object` 키워드로 선언된 객체는 프로그램 실행 동안 메모리에 유지된다. (GC 대상이 아님!)
