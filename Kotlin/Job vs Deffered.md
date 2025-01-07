### 1. Job

코루틴의 생명 주기를 관리하기 위한 핸들러.   
코루틴의 실행 상태를 추적하거나 제어할 수 있음.   

* cancel(): 현재 작업을 취소
* isActive: 코루틴이 실행 중인지 여부를 확인
* join(): 작업이 완료될 때까지 대기


### 2. Deferred

Job의 서브클래스로, 결과 값을 반환할 수 있는 코루틴 작업.   
비동기 작업의 결과를 반환하기 위해 사용.   

* await(): 작업이 완료될 때까지 대기한 후 결과 반환   

---

### Job과 Deferred를 사용하여 비동기 프로세스를 처리하는 방법

1. 작업의 완료 여부를 확인하거나 취소가 중요할 때 --> **Job**   
(예: UI 갱신 작업, 백그라운드 작업)   
   - launch는 항상 **Job**을 반환.  

Job은 주로 작업의 상태를 관리하거나 작업을 취소하는 데 사용한다.   

<br>

2. 작업의 결과 값이 필요할 때 --> **Deferred**   
(예: API 호출 후 데이터 처리, 계산 작업)   
   - async는 항상 **Deferred**를 반환.

Deferred는 작업이 완료된 후 결과를 반환받아 처리할 때 사용한다.   


3. Job과 Deferred를 조합하여 비동기 작업의 상태를 관리하면서 결과 값을 처리할 수도 있다.

---

### 잡을 이용해 API 연속 호출을 취소하는 방법

##### Coroutine과 Job의 취소 기능

Job을 이용해 API 연속 호출을 취소하는 방법은   
이전에 실행된 작업이 아직 완료되지 않은 상태에서 새로운 요청이 들어오면,   
이전 작업을 **cancel()** 호출하여 취소하고   
새로운 작업을 시작하는 방식으로 구현한다.   

짧은 시간 동안 연속 호출을 제한하고 싶다면 **debounce**를 추가할 수 있다.


    + 참고!   

        viewModelScope.launch {
            _photoState.value = null
            try {
                val photos = getSearchPhotos(query)
                _photoState.value = photos
            } catch (e: Exception) {
                e.printStackTrace()
                _photoState.value = emptyList()
            }
        }

위와 같이 하나의 lifecycle dependency가 있는 viewModelScope를 **launch** 해서   
비동기 처리 작업을 하는데, 이게 곧 **Job**이 된다.   

launch 된 Job을 종료할 때 사용하는 것이 **cancel()**   


### CancellationException

Kotlin Coroutine에서 코루틴이 취소될 때 발생하는 정상적인 예외   
즉, 정상적인 취소 메커니즘의 일부이므로 적절히 처리하는 방식으로 접근해야 한다.   

* job.cancel()이 호출되면 **CancellationException**이 발생해 코루틴이 중단됨

CPU를 많이 사용하는 작업이나 긴 루프에서는 **isActive**를 수동으로 확인할 필요는 있다.

---

### async 와 await

**async** 를 이용하여 scope를 열면, **await()**로 결과를 받을 수 있는데,   
이때 반환되는 리턴값이 **Deferred** 이다.   
