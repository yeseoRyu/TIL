자주 쓰는 Git 명령어 모음
===

<br>

* 원격 저장소와 로컬 저장소 state 동일한지 확인

```
    git log origin/[branch]..branch
```

> 아무 log 안 찍히면 동일한 것

<br>

* github에 잘못 올라간 파일 지우기

```
    git rm -r --cached [folder]
```
