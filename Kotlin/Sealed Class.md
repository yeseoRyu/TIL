# Sealed Class란?

같은 파일 내에서만 상속할 수 있도록 제한하는 abstract 클래스!

<br>

```
sealed class Animal {
    data class Dog(val name: String) : Animal()
    data class Cat(val name: String) : Animal()
    object Bird : Animal()
}

fun sound(animal: Animal) {
    when (animal) {
        is Animal.Dog -> println("멍멍!")
        is Animal.Cat -> println("야옹~")
        Animal.Bird -> println("짹짹!")
    }
}
```

<br>

# Sealed Interface

같은 파일 내에서만 상속이 가능한 interface