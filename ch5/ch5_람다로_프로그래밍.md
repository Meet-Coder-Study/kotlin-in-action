# 5. 람다로 프로그래밍

람다 식(lambda expression) 또는 람다는 기본적으로 다른 함수에 넘길 수 있는 작은 코드 조각 을 뜻한다. 람다를 사용하면 쉽게 공통 코드 구조를 라이브러리 함수로 뽑아낼 수 있다.
해당 챕터에서 새롭게 등장한 키워드인 수신 객체 지정 람다(lambda with receiver)가 새로 등장하였다.

---
## 5.1 람다 식과 멤버 참조

람다를 소개하면서 람다와 컬렉션과의 관계, 람다의 문법, 람다를 활용하는 방법 등등을 소개한다.

### 5.1.1 람다 소개: 코드 블록을 함수 인자로 넘기기

일련의 동작을 변수에 저장하거나 다른 함수에 넘겨야 하는 경우가 종종 발생한다. 이전에는 무명 내부 클래스를 통해 해결했지만, 함수형 프로그래밍에서는 함수를 값처럼 다루는 접근 방법을 통해 문제를 해결한다.

클래스를 선언하고 그 클래스의 인스턴스를 함수에 넘기는 대신, 함수형 언어에서는 함수를 직접 다른 함수에 전달할 수 있다. 이를 람다 식을 사용하여 함수를 선언하지 않고, 코드 블록을 직접 함수의 인자로 전달하여 결과를 만들어 낼 수 있다.

### 5.1.2 람다와 컬렉션

코드에서 중복을 제거하는 것은 프로그래밍 스타일을 개선하는 중요한 방법 중 하나이다.
컬렉션을 다룰 때는 대부분의 작업이 몇 가지 일반적인 패턴으로 요약할 수 있고, 그러한 패턴은 라이브러리에서 제공해줘야 하지만, 이러한 라이브러리가 많지 않아, 개발자들이 각자의 방식대로 기능을 직접 구현하곤 했다.

코틀린에서는 이러한 라이브러리 함수를 통해 문제를 쉽게 해결해 준다.

- 컬렉션 직접 검색하기
```kotlin
data class Person(val name: String, val age: Int)  
  
fun findTheOldest(people: List<Person>) {  
    var maxAge = 0 // 가장 많은 나이를 저장
    var theOldest: Person? = null // 가장 연장자를 저장
    for (person in people) {  
        if (person.age > maxAge) {  // 연장자를 비교
            maxAge = person.age  
            theOldest = person  
        }  
    }  
    println(theOldest)  
}  

fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    findTheOldest(people)  
}
```

- 람다를 이용해 컬렉션 검색하기
	- maxBy 함수는 모든 컬렉션을 대상으로 사용이 가능하다.
	- it.age는 비교에 사용할 값을 돌려주는 함수이다.
	- it은 파라미터가 하나뿐이고, 그 타입을 컴파일러가 추론할 수 있는 경우 it을 바로 사용가능하다.
```kotlin
var people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.maxBy(it.age)) // 나이 프로퍼티를 비교, 값이 가장 큰 원소를 찾기
```

> it 사용의 주의사항

it을 사용하는 관습은 코드를 간단하게 만들지만 남용하게 되면 람다가 중첩되어 구현되는 경우 연달아 it이 나오게 되면 코드를 분석하기 위한 시간이 많이 들게 된다.
문맥상 람다 파라미터의 의미나 파라미터의 타입을 쉽게 알 수 없는 경우 파라미터를 명시적으로 선언하는 것이 가독성에 도움이 된다.

- 멤버 참조를 사용하여 컬렉션 검색
```kotlin
people.maxBy(Person::age)
```

### 5.1.3 람다 식의 문법

람다는 값처럼 인자로 사용할 수 있는 동작의 모음으로 정의한다.
람다를 따로 선언하여 변수에 저장할 수도 있는 경우도 존재지만, 대부분은 함수에 인자로 넘기면서 바로 람다를 정의하는 경우가 대부분이다.

- 변수에 저장된 람다를 호출하는 방식
```kotlin
fun main(args: Array<String>) {  
    val sum = { x: Int, y: Int -> x + y }  
    println(sum(1, 2))  
}
```

- 람다 식을 직접 호출하는 방식
```kotlin
fun main(args: Array<String>) {  
    { println(42) }()  
}
```

- 인자로 받은 람다를 실행해 주는 라이브러리 함수 run을 사용하는 방식
```kotlin
fun main(args: Array<String>) {  
    run { println(42) }  
}
```

실행 시점에 코틀린 람다 호출에는 부가 비용이 들지 않기 때문에 성능상에 문제가 없다.

- 표준 라이브러리 joinToString의 사용법을 통한 확인
	- joinToString의 맨 마지막 인자로 함수를 받는다.
	- 람다를 괄호 밖으로 전달하여 작성하는 방식도 가능하다.
```kotlin
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    val names = people.joinToString(separator = " ",  
                          transform = { p: Person -> p.name })  
    println(names)  
}

fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    val names = people.joinToString(separator = " ") {p: Person -> p.name }
    println(names)  
}
```


```kotlin
fun main(args: Array<String>) {  
    val sum = { x: Int, y: Int ->  
       println("Computing the sum of $x and $y...")  
       x + y  
    }  
    println(sum(1, 2))  
}
```

### 5.1.4 현재 영역에 있는 변수에 접근

자바 메서드 안에서 무명 내부 클래스를 정의하는 경우 메서드의 로컬 변수를 무명 내부 클래스에서 사용이 가능하다.
람다를 함수 안에서 정의하면 그 함수의 파라미터 뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용이 가능하다.

- 함수 파라미터를 람다 안에서 사용하는 사례
```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {  
    messages.forEach { // 각 원소에 대한 수행 작업을 람다로 구현
        println("$prefix $it")  // 람다 안에서 함수의 prefix 파라미터를 사용
    }  
}  
  
fun main(args: Array<String>) {  
    val errors = listOf("403 Forbidden", "404 Not Found")  
    printMessagesWithPrefix(errors, "Error:")  
}
```

- 람다 안에서 바깥 함수의 로컬 변수 변경
	- 람다 안에서 사용하는 외부 변수를 람다가 포획한 변수(chapture)라 부른다.
	- 일반적으로 함수 안에 정의된 로컬 변수의 생명주기는 함수가 반환되면 끝이 난다.
	- 하지만 어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장하게 되면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있다.
```kotlin
fun printProblemCounts(responses: Collection<String>) {  
    var clientErrors = 0  
    var serverErrors = 0  
    responses.forEach {  
        if (it.startsWith("4")) {  
            clientErrors++  
        } else if (it.startsWith("5")) {  
            serverErrors++  
        }  
    }  
    println("$clientErrors client errors, $serverErrors server errors")  
}  
  
fun main(args: Array<String>) {  
    val responses = listOf("200 OK", "418 I'm a teapot",  
                           "500 Internal Server Error")  
    printProblemCounts(responses)  
}
```

> Capture 변수의 동작 방식

파이널이 아닌 변수를 포획한 경우 변수를 특별한 래퍼로 감싸, 나중에 변경하거나 읽을 수 있게 한 다음, 래퍼에 대한 참조를 람다 코드와 함께 저장한다.

참고 - p.209

### 5.1.5 멤버 참조

어떠한 메서드의 인자 값에 대해 사용하려는 클라이언트 쪽에서 함수로 정의되어 있는 경우, 함수를 넘길 수 있는 방법에 대한 해결책으로 멤버 참조가 있다.
`::`를 사용하는 식을 멤버 참조라 부른다. 멤버 참조는 프로퍼티나 메서드를 단 하나만 호출하는 함수 값을 만들어준다.

멤버 참조는 멤버를 호출하는 람다와 같은 타입이며, 최상위에 선언된 함수나 프로퍼티도 참조가 가능하다.
- 최상위에 선언된 함수를 참조하는 사례
```kotlin
fun salute() = println("Salute!")  
  
fun main(args: Array<String>) {  
    run(::salute)  
}
```

람다가 인자가 여럿인 다른 함수한테 작업을 위임하는 경우 람다를 정의하지 않고 직접 위임 함수에 대한 참조를 제공하면 편리하다.
```kotlin
var action = { person: Person, message: String -> sendEmail(person, message)}
var nextAction = ::sendEmail // 람다 대신 멤버 참조를 사용
```

생성자 참조(constructor reference)를 사용하면 클래스 생성 작업을 연기하거나 저장해둘 수 있다. :: 뒤에 클래스 이름을 넣으면 생성자 참조를 만들 수 있다.
```kotlin
data class Person(val name: String, val age: Int)  
  
fun main(args: Array<String>) {  
    val createPerson = ::Person  
    val p = createPerson("Alice", 29)  
    println(p)  
}
```

확장 함수도 멤버 함수와 똑같은 방식으로 참조가 가능하다.
```kotlin
fun Person.isAdult() = age >= 21
var predicate = Person::isAdult
```

> 바운드 멤버 참조

코틀린 1.1부터는 바운드 멤버 참조(bound member reference)를 지원한다. 바운드 멤버 참조를 사용하면 멤버 참조를 생성할 때 클래스 인스턴스를 함께 저장한 다음 나중에 그 인스턴스에 대해 멤버를 호출해준다. 따라서 호출 시 수신 대상 객체를 별도로 지정해 줄 필요가 없다.

```kotlin
data class Person(val name: String, val age: Int)  
val p = Person("name", 34)  
// val personAgeFunction = Person::age  // 오류가 남
val dmitrysAgeFunction = p::age  
  
fun main() {  
    // println(personAgeFunction(p))  
    println(dmitrysAgeFunction())  
}
```

personAgeFunction은 인자가 하나이지만 dmitrysAgeFunction은 인자가 없는 함수라는 점 주의


## 5.2 컬렉션 함수형 API

### 5.2.1 필수적인 함수 : filter와 map

```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3, 4)  
    println(list.filter { it % 2 == 0 })  
}
```

```kotlin
data class Person(val name: String, val age: Int)  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    println(people.filter { it.age > 30 })  
}
```

```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3, 4)  
    println(list.map { it * it })  
}
```

```kotlin
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    println(people.map { it.name })  
}
```

```kotlin
fun main(args: Array<String>) {  
    val numbers = mapOf(0 to "zero", 1 to "one")  
    println(numbers.mapValues { it.value.toUpperCase() })  
}
```

### 5.2.2 all, any, count, find: 컬렉션에 술어 적용

```kotlin
data class Person(val name: String, val age: Int)  
  
val canBeInClub27 = { p: Person -> p.age <= 27 }  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 27), Person("Bob", 31))  
    println(people.all(canBeInClub27))  
}
```

```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3)  
    println(!list.all { it == 3 })  
    println(list.any { it != 3 })  
}
```

```kotlin
data class Person(val name: String, val age: Int)  
  
val canBeInClub27 = { p: Person -> p.age <= 27 }  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 27), Person("Bob", 31))  
    println(people.find(canBeInClub27))  
}
```

### 5.2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

```kotlin
data class Person(val name: String, val age: Int)  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 31),  
            Person("Bob", 29), Person("Carol", 31))  
    println(people.groupBy { it.age })  
}
```

```kotlin
fun main(args: Array<String>) {  
    val list = listOf("a", "ab", "b")  
    println(list.groupBy(String::first))  
}
```

### 5.2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

```kotlin
fun main(args: Array<String>) {  
    val strings = listOf("abc", "def")  
    println(strings.flatMap { it.toList() })  
}
```

```kotlin
fun main(args: Array<String>) {  
    val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),  
                       Book("Mort", listOf("Terry Pratchett")),  
                       Book("Good Omens", listOf("Terry Pratchett",  
                                                 "Neil Gaiman")))  
    println(books.flatMap { it.authors }.toSet())  
}
```

## 5.3 지연 계산(lazy) 컬렉션 연산

### 5.3.1 시퀀스 연산 실행: 중간 연산과 최종 연산

```kotlin
fun main(args: Array<String>) {  
    listOf(1, 2, 3, 4).asSequence()  
            .map { print("map($it) "); it * it }  
            .filter { print("filter($it) "); it % 2 == 0 }  
}
```

```kotlin
fun main(args: Array<String>) {  
    listOf(1, 2, 3, 4).asSequence()  
            .map { print("map($it) "); it * it }  
            .filter { print("filter($it) "); it % 2 == 0 }  
            .toList()  
}
```

### 5.3.2 시퀀스 만들기

```kotlin
fun main(args: Array<String>) {  
    val naturalNumbers = generateSequence(0) { it + 1 }  
    val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }  
    println(numbersTo100.sum())  
}
```

```kotlin
fun File.isInsideHiddenDirectory() =  
        generateSequence(this) { it.parentFile }.any { it.isHidden }  
  
fun main(args: Array<String>) {  
    val file = File("/Users/svtk/.HiddenDir/a.txt")  
    println(file.isInsideHiddenDirectory())  
}
```

## 5.4 자바 함수형 인터페이스 활용

### 5.4.1 자바 메서드에 람다를 인자로 전달

### 5.4.2 SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경

#SAM생성자

```kotlin
fun createAllDoneRunnable(): Runnable {  
    return Runnable { println("All done!") }  
}  
  
fun main(args: Array<String>) {  
    createAllDoneRunnable().run()  
}
```

## 5.5 수신 객체 지정 람다: with와 apply

### 5.5.1 with 함수

#with

```kotlin
fun alphabet(): String {  
    val result = StringBuilder()  
    for (letter in 'A'..'Z') {  
         result.append(letter)  
    }    result.append("\nNow I know the alphabet!")  
    return result.toString()  
}  
  
fun main(args: Array<String>) {  
    println(alphabet())  
}
```

- 코틀린에는 함수 호출 시 맨 뒤에 있는 인자가 람다 식이라면 그 람다를 괄호 밖으로 빼낼 수 있다는 문법 관습이 존재한다.
  람다를 밖으로 빼낼 수도 있고, 람다를 괄호 안에 유지해서 함수의 인자임을 분명히 할 수도 있다. 둘 이상의 람다를 인자로 받는 경우 인자 목록의 맨 마지막 람다만 밖으로 뺄 수 있다.
```kotlin
fun alphabet(): String {  
    val stringBuilder = StringBuilder()  
    return with(stringBuilder) {  
        for (letter in 'A'..'Z') {  
            this.append(letter)  
        }  
        append("\nNow I know the alphabet!")  
        this.toString()  
    }  
}  
  
fun main(args: Array<String>) {  
    println(alphabet())  
}
```

```kotlin
fun alphabet() = with(StringBuilder()) {  
    for (letter in 'A'..'Z') {  
        append(letter)  
    }    append("\nNow I know the alphabet!")  
    toString()  
}  
  
fun main(args: Array<String>) {  
    println(alphabet())  
}
```

### 5.5.2 apply 함수

#apply

```kotlin
fun alphabet() = StringBuilder().apply {  
    for (letter in 'A'..'Z') {  
        append(letter)  
    }    append("\nNow I know the alphabet!")  
}.toString()  
  
fun main(args: Array<String>) {  
    println(alphabet())  
}
```

```kotlin
fun alphabet() = buildString {  
    for (letter in 'A'..'Z') {  
        append(letter)  
    }    append("\nNow I know the alphabet!")  
}  
  
fun main(args: Array<String>) {  
    println(alphabet())  
}
```

## 요약