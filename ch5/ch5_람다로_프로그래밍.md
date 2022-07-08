#Java #Kotlin

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

> 의견
> - 바운드 멤버 참조에 대해서는 더 확실한 학습이 필요해 보인다.
> - 현재 내용만 가지고는 와 닿지 않음

## 5.2 컬렉션 함수형 API

함수형 프로그래밍 스타일은 컬렉션을 다룰 때 많이 사용하게 된다. 코틀린에서는 컬렉션을 보다 효과적으로 다루기 위한 표준라이브러리가 존재한다.

여기서는 함수형 API를 활용하되 남용하지 않는 선을 지키면서 이해하기 쉽고 깔끔하게 작성하는 방법에 대해서 다룬다.

### 5.2.1 필수적인 함수 : filter와 map

대부분의 컬렉션은 filter와 map을 통해 처리가 가능하다.
filter는 컬렉션에서 원하는 요소를 제거하는 기능, map은 원소를 변환하는 기능을 제공한다.

- filter 함수의 사용사례
	- 컬렉션을 이터레이션하면서 주어진 람다에 각 원소를 넘겨 람다가 true를 반환하는 원소만 모으는 술어
	- 입력 컬렉션의 원소 중 주어진 술어(predicate)를 만족하는 원소만으로 이루어진 새로운 컬렉션

- 결과가 짝수인 원소만을 새로운 컬렉션으로 만드는 방법
```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3, 4)  
    println(list.filter { it % 2 == 0 })  
}
```

- 나이가 30 초과하는 원소만을 새로운 컬렉션으로 만드는 방법
```kotlin
data class Person(val name: String, val age: Int)  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    println(people.filter { it.age > 30 })  
}
```

- 각 숫자의 제곱이 모인 리스트로 변환
```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3, 4)  
    println(list.map { it * it })  
}
```

- 사람의 리스트가 아닌 이름의 리스트로 변환
```kotlin
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 29), Person("Bob", 31))  
    println(people.map { it.name })  
}
```

#### 람다와 함수형 API를 연계

- 연장자의 이름을 구하는 방법1
	- 먼저 목록에 있는 사람들의 나이의 최댓값을 구하기
	- 그 최댓값과 같은 모든 사람을 반환
```kotlin
people.filter { it.age == people.maxBy(Person::age)!!.age } /* 이 방식은 n^2 */
```

- 연장자의 이름을 구하는 방법2
	- 위 내용에서 불필요하게 반복되는 내용을 별도의 값으로 선언
```kotlin
val maxAge = people.maxBy(Person::age)!!.age /* 최댓값을 한 번만 계산 */
people.filter{ it.age == maxAge}

```

- filter와 변환함수를 map에 적용하는 방법
	- 맵의 경우 키와 값을 처리하는 함수가 따로 존재
		- filterKeys와 mapKeys는 키를 걸러내거나 변환
		- filterValue와 mapValues는 값을 걸러내거나 변환
```kotlin
fun main(args: Array<String>) {  
    val numbers = mapOf(0 to "zero", 1 to "one")  
    println(numbers.mapValues { it.value.toUpperCase() })  
}
```

### 5.2.2 all, any, count, find: 컬렉션에 술어 적용

컬렉션의 모든 원소가 어떤 조건을 만족하였는지 판단하는 연산
- all 또는 any는 특정 조건이 모두 일치하는지 또는 일부 일치하는지 판단
- count는 조건을 만족하는 원소의 개수를 반환
- find 함수는 조건을 만족하는 첫 번째 원소를 반환

- 모든 원소가 특정 술어를 만족하는지 확인하는 all 함수
```kotlin
data class Person(val name: String, val age: Int)  
  
val canBeInClub27 = { p: Person -> p.age <= 27 }   // 27세 이하를 판단하는 술어 선언
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 27), Person("Bob", 31))  
    println(people.all(canBeInClub27))  // 컬렉션의 원소가 모두 만족하는지 확인
		println(people.any(canBeInClub27))  // 컬렉션의 원소가 일부 만족하는지 확인
}
```

조건에 대한 코딩은 가독성을 높이기 위해 any와 all 앞에 !를 붙이지 않는 편이 좋다.
- 가독성을 해치는 코딩 방법
```kotlin
fun main(args: Array<String>) {  
    val list = listOf(1, 2, 3)  
    println(!list.all { it == 3 }) // 모든 원소가 3이 아님
    println(list.any { it != 3 })  // 적어도 하나는 3이 아님
}
```

> 함수를 적재적소에 사용하기 - count, size

- count의 존재를 망각하고 필터링한 크기를 가져오는 비효율적인 코드
	- 조건을 만족하는 모든 원소가 들어가는 중간 컬렉션이 생김
	- count는 조건을 만족하는 원소의 개수만을 추적, 원소를 따로 저장하지 않음
	- filter().size > count()
```kotlin
people.filter(canBeInClub27).size // 내부적으로 중간 컬렉션이 생김
people.count(canBeInClub27) //
```

- 술어를 만족하는 원소를 하나 찾는 find 함수 예시
```kotlin
data class Person(val name: String, val age: Int)  
  
val canBeInClub27 = { p: Person -> p.age <= 27 }  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 27), Person("Bob", 31))  
    println(people.find(canBeInClub27))  // 만족하는 원소가 없는 경우 null을 반환
    println(people.firstOfNull(canBeInClub27))  // null 반환을 명시적으로 표현하려는 경우
}
```

### 5.2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

컬렉션의 모든 원로를 어떤 특성에 따라 여러 그룹으로 나누려는 경우 사용하는 groupBy

- age를 기준으로 별도의 Person 컬렉션을 생성하는 사례
	- groupBy의 결과 타입은 Map<Int, List\<Person\>>
	- 필요한 경우 결과 Map을 mapKeys나 mapValues 등을 사용하여 변경 가능

```kotlin
data class Person(val name: String, val age: Int)  
  
fun main(args: Array<String>) {  
    val people = listOf(Person("Alice", 31),  
            Person("Bob", 29), Person("Carol", 31))  
    println(people.groupBy { it.age })  
}
```

- 멤버 참조(member reference)를 활용하여 문자열을 첫 글자에 따라 분류
	- first는 String의 멤버가 아니라 확장 함수이지만 멤버 참조를 통해 first에 접근가능
```kotlin
fun main(args: Array<String>) {  
    val list = listOf("a", "ab", "b")  
    println(list.groupBy(String::first))  
}
```

### 5.2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

flatMap 하수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고, 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 모은다.

- 두 개의 문자열 리스트를 단일 리스트로 변환하는 방법
```kotlin
fun main(args: Array<String>) {  
    val strings = listOf("abc", "def")  
    println(strings.flatMap { it.toList() })  
}
```

- toSet을 통해 flatMap 결과 리스트에서 중복을 없앤 집합을 만드는 방법
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

위의 map과 filter는 내부적으로 결과 컬렉션을 즉시 생성, 이는 컬렉션 함수를 연쇄하게 되는 경우 매 단계마다 계산 중간 결과를 새로운 컬렉션에 임시로 담게된다.

시퀀스(sequence)는 중간 임시 컬렉션을 사용하지 않고, 컬렉션 연산을 연쇄하는 것이 가능하다.

코틀린 지연 계산 시퀀스는 Sequence라는 인터페이스에서 시작한다.

Sequence안에는 iterator라는 단 하나의 메서드가 존재하여, 해당 메서드를 통해 시퀀스로부터 원소 값을 얻을 수 있다.

Sequence 인터페이스의 강점은 그 인터페이스 위에 구현된 연산에 계산을 수행하는 방법 때문에 생긴다. 시퀀스는 원소를 필요할 때 비로소 계산하여, 중간 처리결과를 저장하지 않고도 연산을 연쇄적으로 적용하여 효율적으로 계산을 수행할 수 있다.

asSequence 확장함수는 어떤 컬렉션이든 시퀀스로 변환하는 것이 가능하다. 시퀀스를 리스트로 변환하기 위해서는 toList를 사용한다.

> 큰 컬렉션에 대해서 연산을 연쇄하려는 경우 시퀀스를 사용하는 것을 규칙으로 삼아라
- 중간 컬렉션을 생성함에도 불구하고 즉시 계산 컬렉션에 대한 연산이 더 효율적인 경우도 존재
- 일반적으로는 큰 컬렉션에서는 중간 원소를 재배열하는 비용이 커지기 때문에 지연 계산이 더 효율적이다.


### 5.3.1 시퀀스 연산 실행: 중간 연산과 최종 연산

시퀀스에 대한 연산은 중간(intermediate) 연산과 최종(terminal) 연산으로 나뉜다.
중간 연산은 다른 시퀀스를 반환하고, 최종 연산은 결과를 반환한다.
결과는 최초 컬렉션에 대해 변환을 적용한 시퀀스로부터 일련의 계산을 수행해 얻을 수 있는 컬렉션이나 원소, 숫자 또는 객체이다.

중간 연산은 항상 지연 계산되고, 최종 연산을 호출하면 비로소 연기되었던 모든 계산이 수행된다.

- 최종 연산이 없는 예시
	- 아무런 출력 결과가 없는 코드
```kotlin
fun main(args: Array<String>) {  
    listOf(1, 2, 3, 4).asSequence()  
            .map { print("map($it) "); it * it }  
            .filter { print("filter($it) "); it % 2 == 0 }  
}
```

- 최종 연산을 호출하여 모든 계산이 수행되는 예시
	- 시퀀스의 모든 연산은 각 원소에 대해서 순차적으로 적용한다.
	- 즉, 첫 번째 원소가 map -> filter 처리 후, 두 번째 원소가 map -> filter 처리되는 방식
```kotlin
fun main(args: Array<String>) {  
    listOf(1, 2, 3, 4).asSequence()  
            .map { print("map($it) "); it * it }  
            .filter { print("filter($it) "); it % 2 == 0 }  
            .toList()  
}
```


> 컬렉션 사용과 시퀀스 사용의 동작 방식차이

- 즉시 계산은 전체 컬렉션에 연산을 적용하지만 지연 계산은 원소를 한번에 하나씩 처리한다，

> 컬렉션에 대한 수행은 연산 순서가 성능에 영향을 끼치는 이유

```kotlin
val people = listOf(
		Person("Alice", 29), 
		Person("Bob", 31), 
		Person("Charles", 31), 
		Person("Dan", 21)
	)

people.asSequence() // map 이후 filter 수행
        .map(Person::name) // 모든 원소에 대한 변환을 수행
        .filter { it.length < 4 }
        .toList();

people.asSequence() // filter 이후 map 수행
        .filter { it.length < 4 } // 부적절한 원소를 먼저 제외
        .map(Person::name)
        .toList();
```

> 자바 스트림과 코틀린 시퀀스 비교
- 자바8을 사용하는 경우 코틀린 컬렉션과 시퀀스에서 제공하지 않는 스트림 연산을 여러 CPU에서 병렬적으로 실행하는 유용한 기능을 사용할 수 있다.

### 5.3.2 시퀀스 만들기

- 시퀀스를 만드는 generateSequence 함수 예시
```kotlin
fun main(args: Array<String>) {  
    val naturalNumbers = generateSequence(0) { it + 1 }  // 초기값 0, ++1 연산 정의
    val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }  
    println(numbersTo100.sum())  // sum이라는 최종 연산 수행시 중간 연산이 지연 연산 수행
}
```

- 객체의 조상으로 이루어진 시퀀스를 만드는 예시
```kotlin
fun File.isInsideHiddenDirectory() =  
        generateSequence(this) { it.parentFile } // 특정 파일의 상위 이렉토리 검색
        .any { it.isHidden } // 숨김 속성을 가진 디렉토리 존재 확인 조건
  
fun main(args: Array<String>) {  
    val file = File("/Users/svtk/.HiddenDir/a.txt")  
    println(file.isInsideHiddenDirectory())  
}
```

## 5.4 자바 함수형 인터페이스 활용

자바에서 메서드에 람다를 넘기기 위해 무명 클래스의 인스턴스를 만들어 넘기는 방식으로 사용했었지만 코틀린에서는 무명 클래스 인스턴스 대신에 람다를 넘기는 것이 가능하다.

인자로 함수형 인터페이스(functional interface) 또는 SAM 인터페이스를 인자 값으로 받는 경우 가능하다. SAM은 단일 추상 메서드(single abstract method)라는 뜻이다.

> 코틀린의 함수 타입 != 자바의 함수형 인터페이스

코틀린에서 함수를 인자로 받을 필요가 있는 함수는 함수형 인터페이스가 아니라 함수 타입을 인자 타입으로 사용해야 한다. 코틀린 함수를 사용할 때는 코틀린 컴파일러가 코틀린 람다를 함수형 인터페이스로 변환해주지 않는다.

### 5.4.1 자바 메서드에 람다를 인자로 전달

함수형 인터페이스를 인자로 원하는 자바 메서드에 코틀린 람다를 전달이 가능하다. 컴파일러는 람다를 인스턴스로 변환하여 전달하게 된다.
여기서 말하는 인스턴스는 특정 인터페이스를 구현한 무명 클래스의 인스턴스라는 뜻이다.

하지만 람다와 무명 객체 사이에는 차이가 존재한다.
객체를 명시적으로 선언하는 경우 메서드를 호출할 때마다 새로운 객체가 생성된다.
람다는 정의가 들어있는 함수의 변수에 접근하지 않는 람다에 대응하는 무명 객체를 메서드를 호출할 때마다 반복 사용한다.

람다가 주변 영역의 변수를 포획(capture variable)한다면 매 호출마다 같은 인스턴스를 사용할 수 없다. 이러한 경우 컴파일러는 매번 주변 영역의 변수를 포획한 새로운 인스턴스를 생성한다.

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

- 람다를 사용하면 다른 함수에 인자로 넘길 수 있다.
- 람다가 함수 인자의 경우, 괄호 밖으로 람다를 빼낼 수 있다.
- 람다의 인자가 단 하나뿐인 경우, 인자 이름을 지정하지 않고 it이라는 디폴트 이름 사용가능
- 람다 안에 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓸 수 있다.
- 메서드, 생성자, 프로퍼티의 이름 앞에 ::을 붙이면 각각에 대한 참조를 만들 수 있다.
- 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산을 조합할 수 있다.
- 수신 객체 지정 람다를 사용하면, 람다 안에서 미리 정해둔 수신 객체의 메서드를 직접 호출 가능
- 표준 라이브러리의 with 함수를 사용하면 어떤 객체에 대한 참조를 작성하지 않고 객체의 메서드를 호출가능
- apply를 사용하면 어떤 객체라도 빌더 스타일 API를 사용하여 생성하고 초기화 가능