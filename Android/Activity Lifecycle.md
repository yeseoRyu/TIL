# Activity LifeCycle

Activity 클래스는 6가지 콜백으로 이루어진 핵심 집합을 제공한다.

![activity_lifecycle](../images/ActivityLifecycle.png)

> 그림1 | Activity 생명주기

<br>

* 최초로 앱을 실행하면 `onCreate()`가 호출되는데 setContentView를 통해 View를 inflate한다. 이 콜백은 Activity가 메모리상에 올라갈 때 실행되기 때문에 Activity가 만들어지면서 최초에 단 한 번 실행되기 때문에 **View와 관련된 작업이나 리소스 초기화같은 작업** 을 하면 좋다.

* 다음으로 `onStart()`가 호출되는데 이 시점부터 사용자가 Activaty를 볼 수 있다. 그리고 액티비티가 실제 사용자와 상호작용이 가능한 foreground에 위치하면 `onResume()`이 호출된다. 이상태를 Activity가 실행 중인 것으로 본다.

* 반대로 Activity가 실행 중인 상태에서 사용자와 상호작용이 불가능한 상태, 즉 포커스를 잃은 상태가 되면 `onPouse()`가 호출된다.

* `onStop()`은 foeground에 있던 Activity가 background로 이동하여 더 이상 보이지 않을 때 호출된다. 시스템에서 메모리 부족시 background 상에 존재하는 앱들을 강제로 종료할 수 있기 때문에 **메모리에 할당된 리소스들 중 중요한 리소스들의 해제** 는 이 시점에 하는 것이 좋다.

* 그리고 Activity가 종료되거나 앱 프로세스 자체가 종료되면 `onDeatroy()`가 호출된다. 이 콜백이 호출되면 해당 Activity는 스택에서 pop된다.

<br><br>

## Activity State

![lifecycle_state](../images/lifecycle_state.png)

> 그림2 | Android 활동 수명 주기를 구성하는 상태 및 이벤트

<br>

### 기본적인 액티비티 전환

* 최초에 Activity를 실행시키면 onResume까지 호출되면서 `RESUMED` 상태에 도달한다. 이 후, 새로운 Activity로 전환하면 기존 Activity는 포커스를 잃고 백그라운드로 전환되고, 새로운 Activity는 Activity 스택 최상단에 위치하면서 화면에 표시된다. 다시 기존 Activity로 전환하면 새로운 Activity는 Activity 스택에서 pop되기 때문에 `DESTROYED` 상태에 도달하면서 메모리에서 해제된다.

<br>

<h3> Dialog </h3>

Dialog는 작은 window이다. 즉, Activity와는 별개의 화면 구성 요소이기 때문에 Activity위에 Dialog가 생성되어도 스택에 push되지 않는다. Activity는 여전히 `RESUMED` 상태이다.

<br>

### 홈버튼을 눌러 바탕화면 진입시 어떤 라이프사이클의 상태가 될까?

* 홈 버튼을 누르면 앱이 백그라운드로 이동하는데, 사용자가 앱을 떠나기 직전에 `onPause()` 호출

* 앱이 화면에서 완전히 보이지 않게 되면 `onStop()` 호출

    - 이 단계에서 무거운 **리소스를 해제하고 상태를 저장**할 수 있다.

* 이때 시스템에 의해 프로그램이 종료되지 않는다면 `onDestroy()`는 호출되지 않는다 !

<br>

### Activity가 파괴되었을 때 데이터를 저장 및 복원하는 방법은?

* SavedState

* Activity가 종료되기 전에 `Activity.onSaveInstanceState(Bundle)`가 호출되는데, 이를 통해 다시 돌아올 때 상태를 복원할 수 있다.

> Fragment도 동일한 메서드가 있다.

<br>

```
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    isEditing = savedInstanceState?.getBoolean(IS_EDITING_KEY, false)
    randomGoodDeed = savedInstanceState?.getString(RANDOM_GOOD_DEED_KEY)
            ?: viewModel.generateRandomGoodDeed()
}
```

* 액티비티가 다시 생성될 때 매개변수로 `Bundle`이 전달되는 것을 볼 수 있다. 바로 이 객체로부터 상태를 복원하는 것이다 !

<br>

### A 액티비티 위에 B 액티비티가 떴을 때의 라이프사이클 변화

* A 액티비티가 실행 중이고, B 액티비티를 시작하면 A는 background로 이동하고 B는 foreground가 된다.

* B 액티비티가 시작될 때 (A → B)

  - A.onPause()
  - B.onCreate()
  - B.onStart()
  - B.onResume()
  - A.onStop() (B가 전체 화면일 경우)

* B 액티비티에서 뒤로 가기(Back 버튼) (B → A)

  - B.onPause()
  - A.onRestart()
  - A.onStart()
  - A.onResume()
  - B.onStop()
  - B.onDestroy() (B가 종료됨)

* B가 종료되고 A가 다시 foreground가 된다.

* A는 onRestart(), onStart(), onResume()을 거쳐 다시 running.

<br><br>

# 싱글 액티비티(SAA)와 프래그먼트의 중요성

* 싱글 액티비티 구조는 하나의 액티비티가 앱의 모든 화면을 관리하는 구조이다. 이 구조는 앱의 복잡성을 줄이고, 관리를 용이하게 한다.

* 액티비티 간의 전환 없이 프래그먼트를 교체하는 방식으로 화면을 전환할 수 있다. 이는 앱의 **반응 속도를 향상**시키고 **UX를 개선**할 수 있다.

* 또한, 싱글 액티비티 구조는 앱의 생명주기 관리를 단순화시키며, **리소스 사용을 최적화**한다. 여러 액티비티를 사용하는 구조에 비해 **메모리 소비가 적기 때문**이다.

<br>

> 싱글 액티비티 구조의 구현은 안드로이드 Jetpack의 Navigation Component와 같은 현대적인 도구를 사용하여 더욱 쉽고 효율적으로 할 수 있다.

<br><br>

## 프래그먼트 활용과 관리

* 프래그먼트는 독립적인 생명주기를 가지며, 다양한 화면 구성과 재사용성을 제공합니다. 이는 앱의 성능 최적화와 메모리 관리에도 긍정적인 영향을 미친다.

* 프래그먼트 간의 데이터 전달과 상호작용은 인터페이스나 ViewModel을 통해 이루어질 수 있다. 이는 프래그먼트 간의 결합도를 낮추고, 코드의 재사용성을 높인다.

<br>

> 프래그먼트의 생명주기 관리는 앱의 성능과 안정성에 직접적인 영향을 미친다. 따라서, 프래그먼트의 생명주기 이벤트를 적절히 관리하는 것이 중요하다 !

<br><br>

# Fragment Lifecycle

Fragment에는 더 많은 수명 주기 메서드가 있다. 수명 주기를 관리하기 위해 Fragment는 LifecycleOwner를 구현하여, getLifecycle() 메서드를 통해 액세스할 수 있는 Lifecycle 객체를 노출한한다.

![fragment_lifecycle](../images/fragment_lifecycle.png)

> 그림3 | 프래그먼트 Lifecycle 상태와 프래그먼트의 수명 주기 콜백 및 프래그먼트의 뷰 Lifecycle와의 관계

<br>
