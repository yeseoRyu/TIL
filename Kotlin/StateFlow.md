StateFlow
===

StateFlow는 관찰 가능한 변경 가능 상태를 유지해야 하는 클래스에 아주 적합하다.   
MutableStateFlow 업데이트를 발생하면, StateFlow에서 수집된다.

  - 상태를 저장하고 구독자에게 최신 상태를 제공하는 핫 플로우
  - 항상 하나의 값을 유지
  - 사용 예: UI 상태 관리

<br>

## shareIn을 사용하여 Colde Flow을 Hot Flow으로 만들기

    fun <T> Flow<T>.stateIn(
        scope: CoroutineScope, 
        started: SharingStarted, 
        initialValue: T
    ): StateFlow<T>


이 연산자를 사용하면, Flow(콜드스트림)를 주어진 코루틴 스코프에서 시작되는 StateFlow(핫스트림)로 변경한다. 단일로 실행되는 업스트림 플로우에서 가장 최근에 내보낸 값을 여러 다운스트림 구독자들이 공유하는 형태다.


> 참고: 콜드 플로우는 on-demand 방식으로 생성되며 관찰 중일 때 데이터를 방출한다. 핫 플로우는 항상 활성화되어 있으며 관찰 여부에 관계없이 데이터를 방출할 수 있다.

1. Cold Flow
구독자가 있을 때만 데이터 스트림을 생성하고 실행되는 플로우

* 특징
    * Lazy(지연 실행)
        - 구독자가 생길 때까지 실행되지 않는다.
    * 독립적인 스트림
        - 각 구독자는 독립적으로 데이터를 받는다.
    * 데이터는 구독자가 새로 요청할 때마다 생성
    * 기본적인 Flow는 콜드 플로우이다.


2. Hot Flow
데이터가 항상 활성 상태로 존재하며, 구독자가 없더라도 데이터를 생성할 수 있으며, 구독자 간에 **공유**된다.

<br>

## SharingStarted의 주요 키워드와 동작

* SharingStarted
  - 플로우의 시작과 정지 시점을 제어하기 위한 정책을 정의

* Eagerly
  - 플로우 즉시 시작 - 항상 최신 데이터 유지

* Lazily
  - 플로우 최초 구독 시 시작 - 리소스 절약

* WhileSubscribed(stopTimeoutMillis)
  - 구독자가 있을 때만 플로우를 유지하며, 구독자가 모두 사라지면 일정 시간 후 플로우를 정지

WhileSubscribed 공유 정책은 수집기가 없을 때 업스트림 흐름을 취소하는 데 사용됩니다. 이러한 방식으로 아무도 위치 업데이트에 관심이 없을 때 리소스 낭비를 방지할 수 있습니다.

> WhileSubscribed(5000)를 사용하면 마지막 수집기가 사라진 후에도 대부분의 시간 동안 업스트림 흐름을 5초 더 활성화할 수 있다. 이렇게 하면 구성 변경과 같은 특정 상황에서 업스트림 흐름을 재시작할 수 없다. 이 팁은 업스트림 흐름을 생성하는 데 비용이 많이 들고 ViewModels에서 이러한 연산자를 사용할 때 특히 유용하다.

<br>

### LiveData와의 차이
StateFlow와 LiveData는 둘 다 관찰 가능한 데이터 홀더 클래스이며, 앱 아키텍처에 사용할 때 비슷한 패턴을 따른다는 공통점이 있다.   

* 차이점
  - StateFlow의 경우 초기 상태를 생성자에 전달해야 하지만 LiveData의 경우는 그렇지 않다.
  - 뷰가 STOPPED 상태가 되면 LiveData.observe()는 소비자를 자동으로 등록 취소하는 반면, StateFlow 또는 다른 흐름에서 수집하는 경우 자동으로 수집을 중지하지 않는다. 동일한 동작을 실행하려면 Lifecycle.repeatOnLifecycle 블록에서 흐름을 수집해야 한다. 

<br>

### SharedFlow 와의 차이점
* SharedFlow는 StateFlow의 유연한 구성 일반화

  - 데이터의 브로드캐스트를 지원하는 핫 플로우
  - 여러 구독자에게 데이터를 동시에 전달
  - 사용 예: 이벤트 스트림 처리