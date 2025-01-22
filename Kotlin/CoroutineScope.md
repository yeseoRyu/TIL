CoroutineScope
===

### 1. 코루틴 수명관리
coroutineScope는 비차단이며, 호출하는 스레드를 블록하지 않는다. 

* async를 사용하여 여러 비동기 작업을 실행할 경우, 각각의 코루틴이 자식 코루틴으로 생성된다.
* coroutineScope는 이 자식 코루틴들이 모두 완료되기 전까지 종료되지 않는다.
* 따라서, 모든 async 블록이 끝나기 전에 함수가 종료되는 것을 방지할 수 있다.


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


* unplashRepository.getSearchPhotos(query) api 호출 후 결과 results
* mapNotNull 로 null을 제외한 List 구성
* async & awaitAll 로 병렬적으로 함수 호출 후 작업 완료시까지 대기 --> 한 번에 모두 반환
  - 이떄 coroutineScope 로 감싸지 않으면, async로 실행된 각각의 작업 중 하나의 예외가 전체 작업을 중단하거나 누락된 작업이 발생할 수 있다.


#### 요약
1. 모든 자식 코루틴이 완료되기 전에는 함수가 종료되지 않는다.
2. 예외가 발생하더라도 전체 작업 흐름이 관리된다.
3. 구조화된 동시성을 통해 안정성과 가독성을 높인다.