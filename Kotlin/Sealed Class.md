# Sealed Class란?

특정 클래스들만 상속할 수 있도록 제한하는 abstract 클래스!

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
-

<br>