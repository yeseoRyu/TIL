async await
===
async 키워드는 함수 앞에 사용되며, 해당 함수가 비동기 함수임을 나타낸다.   
비동기 함수 내부에서는 await 키워드를 사용하여 프로미스의 결과를 기다릴 수 있으며, 이는 프로미스가 완료될 때까지 함수의 실행을 일시 중지하고, 결과가 준비되면 다시 실행을 계속합니다.

<br>

    public fun <T> CoroutineScope.async(
        context: CoroutineContext = EmptyCoroutineContext,
        start: CoroutineStart = CoroutineStart.DEFAULT,
        block: suspend CoroutineScope.() -> T
    ): Deferred<T> {
        val newContext = newCoroutineContext(context)
        val coroutine = if (start.isLazy)
            LazyDeferredCoroutine(newContext, block) else
            DeferredCoroutine<T>(newContext, active = true)
        coroutine.start(start, coroutine, block)
        return coroutine
    }

코루틴 스코프를 열어서 async로 suspend fun 같이 비동기 작업을 수행하는 것을 볼 수 있다.   
이때 반환값은 Deferred<T> 이다.   

<br>

    private suspend fun getSearchPhotos(query: String): List<SinglePhoto> {
        return coroutineScope {
            try {
                unplashRepository.getSearchPhotos(query)
                    .results
                    ?.mapNotNull { result ->
                        async {
                            result?.takeIf { it.description != null }?.id?.let { photoId ->
                                runCatching {
                                    unplashRepository.getSinglePhotoById(photoId)
                                }.getOrNull()
                            }
                        }
                    }?.awaitAll()?.filterNotNull() ?: emptyList()
            } catch (e: Exception) {
                e.printStackTrace()
                emptyList()
            }
        }
    }

<br>

* 검색어 query에 대한 getSearchPhotos()의 results를 기반으로 (mapNotNull로 null값 제외) description이 있는 결과값을 필터링하여 반환되는 id를 각각 getSinglePhotoById()로 보낸다. 이때 하나하나의 값이 플랫하게 진행되는 게 아니라, 병렬적으로 진행하고(Non-Blocking) awaitAll()로 한거번에 List에 모두 담겨진 호출 결과를 가져올 수 있다.

* runCatching 는 지정된 함수 블록을 호출하고 호출이 성공하면 캡슐화된 결과를 반환하고, 에러 발생시 catch해서 null로 보낸다.(getOrNull) 이로써 코루틴 사용시 발생되는 예외처리를 자동으로 지원하여 유용하다.

<br>

* async의 반환값 Deferred<T> await()로 받기기
    - 반환값이 있는 비동기 통신
    - 플랫하지 않고 병렬적으로 통신된다.
    - 반환값이 있을 때 api 통신의 성능을 높이기 위해 async를 사용한다!
    - scope 안에 있는 것을 잠시 들고 다른일을 하다가 await가 불리면 반환값으로 주겠다.(예약 개념)
    - Non-Blocking

* 하나의 일련의 행동 -- > Job (launch)   
  functional Language 가 Job이고 그 안에 비동기적 통신이 필요하다 --> Job을 사용함
