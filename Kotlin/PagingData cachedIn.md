# PagingData

```
    val items: Flow<PagingData<UiItem>> = _apiPhotoState.asFlow()
        .map { state -> state ?: emptyList() } // null 처리
        .flatMapLatest { photos ->
            Pager(
                config = PagingConfig(pageSize = 20, enablePlaceholders = false),
                pagingSourceFactory = { PhotoPagingSource(photos) }
            ).flow
        }
        .cachedIn(viewModelScope)
        .combine(savedPhotos) { pagingData, savedPhotos ->
            pagingData.map { photo ->
                val savedPhoto = savedPhotos.find { it.id == photo.id }
                mapToUiItem(photo, savedPhoto)
            }
        }
```

<br><br>

## Flow 결합하기

* 두 개의 Flow를 결합해서 하나의 결과를 만들고 싶을 때 combine을 사용한다. 여기서는 pagingData와 savedPhotos라는 두 Flow를 결합하였다.
