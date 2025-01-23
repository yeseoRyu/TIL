UI 갱신 시 setValue와 postValue의 차이
===
Observer 패턴으로 데이터 변화를 관찰하여 UI 업데이트 할 때, ViewModel에서 LiveData의 상태 변화를 적용한다. 데이터 값을 적용하는 방식에 setValue와 postValue가 있는데, 이 두 방법의 차이가 무엇일까?

<br>

> 참고 : LiveData는 주어진 라이프사이클 내에서 관찰할 수 있는 데이터 홀더 클래스. Observer와 쌍으로 추가될 수 있으며, LifecycleOwner가 활성 상태일 때 래핑된 데이터의 수정에 대해 알림을 받는다.

<br><br>

## PostValue 내부 동작

```
    protected void postValue(T value) {
        boolean postTask;
        synchronized (mDataLock) {
            postTask = mPendingData == NOT_SET;
            mPendingData = value;
        }
        if (!postTask) {
            return;
        }
        ArchTaskExecutor.getInstance().postToMainThread(mPostValueRunnable);
    }
```

* postValue의 내부 동작을 보면 ***synchronized*** 블럭으로 감싸져 있는 것을 확인할 수 있다. 이는 곧 Lock을 걸고 백그라운드에서 ***비동기 처리***가 된다는 것을 의미한다. 그 후 postToMainThread()로 메인 스레드에 전달한다.

<br>

> Note!※ UI를 그릴 때에는 모두 메인 스레드에서 돌아가게 되어 있는데, postValue를 사용하면 비동기 통신으로 메인 스레드 UI가 그려지게 된다. 이렇게 되면 플랫하게 동기나 블로킹 형식이 아니게 되기 때문에 예상치 못한 값을 전달받을 수 있으며(적용된 값이 차근차근 적용되지 않고 순서가 꼬여버리는 등), 관리가 어려워질 수 있다.

<br><br>

## UI 갱신 최적화 방법은?

* setValue는 UI 스레드에서만 호출이 가능하다. 이는 곧 ***오직 메인 스레드***에서 값을 즉각적으로 처리한다는 것을 의미한다. UI 작업같은 경우 동기적으로 플랫하게 관리하는 것이 일반적이므로 setValue를 권장한다.

*  postVlaue를 사용하기 좋은 상황도 있다. ***대량 데이터 처리***가 필요할 경우이다. 이 경우엔 데이터가 짧은 시간 내 반복적으로 변경될 수 있으므로, setValue 대신 postValue를 사용함으로써 메인 스레드의 부담을 줄이고, 데이터 업데이트를 효율적으로 처리할 수 있다.