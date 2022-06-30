# 3장. 함수 정의와 호출

- 컬렉션, 문자열, 정규식을 다루기 위한 함수
- 이름 붙인 인자, 디폴트 파라미터 값, 중위 호출 문법 사용
- 확장 함수와 확장 프로퍼티를 사용해 자바 라이브러리 적용
- 최상위 및 로컬 함수와 프로퍼티를 사용해 코드 구조화

## 3.1 코틀린에서 컬렉션 만들기

**코틀린은 자체적인 컬렉션을 제공하지 않는다**. → 자바 컬렉션 사용

왜?

- 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기가 훨씬 더쉽다.
    - 자바에서 코틀린 함수 호출하거나 코틀린에서 자바 함수를 호출할 때 서로 변횐할 필요가 없다.
- 코틀린 컬렉션은 자바 컬렉션보다 더 많은 기능을 쓸 수 있다. (확장 함수를 활용)

```jsx
val set = hashSetOf(1, 7, 53)
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

fun main(args: Array<String>) {
    println(set.javaClass)
    println(list.javaClass)
    println(map.javaClass)
}
```

```jsx
val strings = listOf("first", "second", "fourteeth")
println(strings.last()) // fourteeth
 
val numbers = setOf(1, 14, 2)
println(numbers.max()) // 14
```

## 3.2 함수를 호출하기 쉽게 만들기

```jsx
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(list)
}
```

```jsx
[1, 2, 3]
```

위와 같은 toString() 결과를 바꾸고 싶을 때, joinToString 함수를 새로 만든다.

### 3.2.1 이름 붙인 인자

```jsx
fun <T> joinToString(
        collection: Collection<T>,
        separator: String,
        prefix: String,
        postfix: String
): String {

    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(joinToString(list, "; ", "(", ")"))
}
```

```jsx
(1; 2; 3)
```

코틀린으로 작성한 함수를 호출할 때 함수에 전달하는 인자 중 일부(혹은 전부) 이름을 명시할 수 있다. 호출 시 인자 중 어느 하나라도 명시한 경우 혼동을 막기 위해 모든 인자 이름을 꼭 명시하자.

### 3.2.2 디폴트 파라미터 값

자바에서는 일부 클래스에서 오버로딩한 메서드가 많아진다는 문제.

코틀린에서는 함수 선언에서 파라미터의 디폴트 값을 지정할 수 있으므로 이런 오버로드 중 상당수를 피할 수 있다. 

```jsx
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
//    println(list.joinToString(separator = "; ", prefix = "(", postfix = ")"))
    println(list.joinToString(prefix = "(", postfix = ")"))
}
```

- 디폴트 파라미터의 값은 함수를 호출하는 쪽이 아니라 **함수 선언 쪽에서 지정**된다.
- 일반 호출 문법을 사용하려면 함수를 선언할 때와 같은 순서로 인자를 지정해야 한다. 이름 붙은 인자를 사용하는 경우에는 인자 목록의 중간에 있는 인자를 생략하고, 지정하고 싶은 인자를 이름에 붙여서 순서와 관계없이 지정할 수 있다.

> 디폴트 값과 자바
>
>`@JvmOverloads` 어노테이션을 함수에 추가하면 코틀린 컴파일러가 자동으로 맨 마지막 파라미터로부터 파라미터를 하나씩 생략한 오버로딩 자바 메서드를 추가해줌.
 

```jsx
println(list.joinToString(prefix = "(", postfix = ")"))
```

```jsx
(1, 2, 3)
```

일부를 생략할수도 있음. 생략 하면 안쓴거는 디폴트 값으로 사용

### 3.2.3 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티

어느 한 클래스에 포함시키기 어려운 코드. 중요한 객체는 하나 뿐이지만 그 연산을 객체의 인스턴스 API에 추가해서 API를 너무 크게 만들고 싶지 않은 경우.

→ 다양한 정적 메서드를 모아두는 역할만 담당하며, 특별한 상태나 인스턴스 메서드는 없는 클래스가 생겨난다.

- JDK의 Collections 클래스, XXXUtil

함수와 프로퍼티는 클래스 밖에 최상위에 위치시킬 수 있다.

### 최상위 함수

함수를 최상위로 선언하면 컴파일 할 때 자동으로 새로운 클래스를 정의해줌. 

클래스 이름은 최상위 함수가 들어있던 코틀린 소스 파일 이름과 동일하게 생성

만약 이름을 변경하고 싶다면 파일에  `@JvmName` 애너테이션 추가

```jsx
package strings

// join.kt
fun joinToStirng() : String { ... }package strings

// join.kt
fun joinToStirng() : String { ... }
```

- JVM에서 컴파일러가 이 파일을 컴파일할 때 새로운 클래스를 정의해준다.


```jsx
// Java
package strings;

public class JoinKt {
	public static String joinToString(...) { ... } 
}
```

### 최상위 프로퍼티


- 코틀린 컴파일러가 생성하는 클래스 이름은 최상위 함수가 들어있던 코틀린 소스 파일의 이름과 대응한다.

```kotlin
const val UNIX_LINE_SEPARATOR = "\n"
```

```kotlin
public static final String UNIX_LINE_SEPARATOR = "\n";
```

프로퍼티를 최상위에 놓으면 다른 필드들과 동일하게 public getter를 통해 접근이 가능함. 만약 상수처럼 사용하고 싶다면 `const val` 사용 시 `public static final String` 처럼 사용 가능함.

## 3.3 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티

### 확장 함수

- 어떤 클래스와 멤버 메소드인 것처럼 호출할 수 있지만 그 클래스 밖에 선언된 함수
- 확장 함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙인다.
    - 클래스 이름: 수신 객체 타입 / 확장함수가 호출하는 대상: 수신 객체

![image](https://user-images.githubusercontent.com/37948906/175299221-7410e529-089a-4846-b915-7496c972b628.png)

- 원하는 메서드를 클래스에 추가할 수 있다. 심지어 그루비언어와 같은 다른 JVM 언어로 작성된 클래스도 확장 가능
- 확장 함수 안에서는 클래스 내부에서만 사용할 수 있는 private, protected 멤버는 사용할 수 없음.

```jsx
fun String.lastChar(): Char = this.get(this.length - 1)

fun main(args: Array<String>) {
    println("Kotlin".lastChar())
}
```

### 3.3.1 임포트와 확장 함수

- 확장 함수를 사용하려면 그 함수를 다른 클래스나 함수와 마찬가지로 임포트 해야 함.
- as 키워드를 사용하면 임포트한 클래스나 함수를 다른 이름으로 부를 수 있음.

### 3.3.2 자바에서 확장 함수 호출

확장 함수를 StringUtil.kt 파일에 정의했다면 자바 파일에서도 `char c = StringUtilKt.lastChar("Java");` 와 같이 호출 가능

### 3.3.3 확장 함수로 유틸리티 함수 정의

```jsx
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = arrayListOf(1, 2, 3)
    println(list.joinToString(" "))
}
```

마치 joinToString이 list의 확장 함수처럼 사용할 수 있음.

확장 함수는 정적 메소드 호출에 대한 문법적인 편의이다.

### 3.3.4 확장 함수는 오버라이드할 수 없다.

```jsx
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

fun main(args: Array<String>) {
    val view: View = Button()
    view.click()
}
```

```jsx
Button clicked
```

Button은 View의 하위 타입이기 때문에 View = Button()과 같이 대입할 수 있다. 이 때 click은 Button 클래스가 오버라이드했다면 실제로는 Button이 오버라이드한 click이 호출된다.

```jsx
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

fun main(args: Array<String>) {
    val view: View = Button()
    view.showOff()
}
```

```jsx
I'm a view!
```

위와 같이, 확장 함수는 정적으로 결정되기 때문에 view의 실제 객체 타입은 Button이지만, view타입이 View이기 때문에 View에 정의된 확장 함수가 호출된다.

즉, 변수의 정적 타입에 의해 어떤 확장 함수가 호출될지 결정되지, 그 변수에 저장된 동적인 타입에 의해 확장 함수가 결정되지 않는다.

> 어떤 클래스를 확장한 함수와 그 클래스의 멤버 함수의 이름과 시그니처가 같다면 확장 함수가 아니라 멤버 함수가 호출된다. (멤버 함수의 우선순위가 더 높다)
> 

### 3.3.5 확장 프로퍼티

확장 프로퍼티

```jsx
val String.lastChar: Char
    get() = get(length - 1)
```

변경 가능한 확장 프로퍼티

```jsx
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }
```

실제 사용 시 아래와 같이 나온다.

```jsx
fun main(args: Array<String>) {
    println("Kotlin".lastChar)

    val sb = StringBuilder("Kotlin?")
    sb.lastChar = '!'
    println(sb)
}
```

```jsx
n
Kotlin!
```

자바에서 확장 프로퍼티를 사용하고 싶다면 게터 세터를 명시적으로 호출해야 한다.
`StringUtilKt.getLastChar(”Java”)`

## 3.4 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원

- vararg 키워드: 가변 인자로 받을 수 있는 함수를 정의 가능
- 중위 함수 호출 구문: 인자가 하나 뿐인 메서드를 간편하게 호출
- 구조 분해 선언: 복잡한 값을 분해해서 여러 변수에 나눠 담을 수 있음

### 3.4.1 자바 컬렉션 API 확장

어떻게 자바 라이브러리 클래스의 인스턴스인 컬렉션에 대해 코틀린이 새로운 기능을 추가할 수 있을까? → last, max 등 코틀린에서 지원하는 언어는 모두 `확장함수` 이다!

### 3.4.2 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의

```jsx
fun listOf<T> (vararg values: T) : List<T> {...}
```

자바의 가변인자인 ‘…’ 처럼 vararg 를 사용할 수 있다.

혹은 그냥 배열로 넘기되 배열 앞에 *만 붙여서 배열의 각 원소를 명시적으로 풀어서 사용할 수 있도록 *args로 스프레드 연산자를 붙여주어 사용할 수 있다.

```jsx
fun main(args: Array<String>) {
    val list = listOf("args: ", *args)
    println(list)
}
```

### 3.4.3 값의 쌍 다루기: 중위 호출과 구조 분해 선언

### 중위 호출

to 라는 키워드는 중위 호출이라는 방식으로 to라는 일반 메서드를 호출한 것.

![image](https://user-images.githubusercontent.com/37948906/175299291-644545a8-728e-4554-b622-93b6e79b1b9a.png)

인자가 하나 뿐인 확장 함수는 괄호를 생략하고 중위 호출 방식으로 사용할 수 있음. 이 때, 함수를 중위 호출에 사용할 수 있게 하고 싶다면 infix 변경자를 함수 앞에 붙인다.

```jsx
infix fun Any.to(other: Any) = Pair(this, other)
```

### 구조 분해

```jsx
for ((index, element) in collection.withIndex()) {
  println("$index: $element")
}
```

위와 같이  컬렉션의 원소를 인덱스와 값을 분해해서 따로 담을수도 있음.

## 3.5 문자열과 정규식 다루기

### 3.5.1 문자열 나누기

```jsx
fun main(args: Array<String>) {
    println("12.345-6.A".split("\\.|-".toRegex()))
}
```

regex를 잘 몰라도 여러 구분 문자열을 지정할 수 있는 spit 확장 함수를 오버로딩한 버전도 있다.

```jsx
fun main(args: Array<String>) {
    println("12.345-6.A".split(".", "-"))
}
```

```jsx
[12, 345, 6, A]
```

### 3.5.2 정규식과 3중 따옴표로 묶은 문자열

```jsx
fun parsePath(path: String) {
    val directory = path.substringBeforeLast("/")
    val fullName = path.substringAfterLast("/")

    val fileName = fullName.substringBeforeLast(".")
    val extension = fullName.substringAfterLast(".")

    println("Dir: $directory, name: $fileName, ext: $extension")
}
```

```jsx
fun parsePath(path: String) {
    val regex = """(.+)/(.+)\.(.+)""".toRegex()
    val matchResult = regex.matchEntire(path)
    if (matchResult != null) {
        val (directory, filename, extension) = matchResult.destructured
        println("Dir: $directory, name: $filename, ext: $extension")
    }
}
```

```jsx
fun main(args: Array<String>) {
    parsePath("/Users/yole/kotlin-book/chapter.adoc")
}
```

```jsx
Dir: /Users/yole/kotlin-book, name: chapter, ext: adoc
```

![image](https://user-images.githubusercontent.com/37948906/175299349-64a45599-598c-49c6-890f-a66c72c1f7bb.png)

이와 같이 삼중 따옴표를 통해 이스케이프 문자도 신경 안쓸 수 있음.

### 3.5.3 여러 줄 3중 따옴표 문자열

```jsx
val kotlinLogo = """| //
                   .|//
                   .|/ \"""
```

여러줄도 그대로 표현하기 가능

## 3.6 코드 다듬기: 로컬 함수와 확장

함수에서 추출한 함수를 원 함수 내부에 중첩 가능

만약 아래와 같이 중복된 코드가 있을 때는 어떻게 할까?

```jsx
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {
    if (user.name.isEmpty()) {
        throw IllegalArgumentException(
            "Can't save user ${user.id}: empty Name")
    }

    if (user.address.isEmpty()) {
        throw IllegalArgumentException(
            "Can't save user ${user.id}: empty Address")
    }

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}
```

아래와 같이 로컬 함수를 사용하면 간단해진다.

```jsx
class User(val id: Int, val name: String, val address: String)

fun saveUser(user: User) {

    fun validate(user: User,
                 value: String,
                 fieldName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException(
                "Can't save user ${user.id}: empty $fieldName")
        }
    }

    validate(user, user.name, "Name")
    validate(user, user.address, "Address")

    // Save user to the database
}

fun main(args: Array<String>) {
    saveUser(User(1, "", ""))
}
```

## 요약

- 코틀린은 자체 컬렉션 클래스 X, 자바 클래스 확장 API 제공
- 함수 파라미터의 디폴트 값 정의 → 함수 오버로딩 정의 필요성이 줄어든다.
- 이름 붙인 인자 사용 → 함수 인자가 많을 때 호출 가독성 향상 가능
- 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티 선언 가능
- 확장 함수와 프로퍼티 사용 → 외부 라이브러리에 정의된 클래스와 모든 API를 확장해서 사용 가능
- 중위 호출을 통해 인자가 하나 밖에 없는 메소드나 확장 함수 사용 가능
- 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리 함수 제공
- 자바 문자열 표현 시 3중 따옴표 문자열 사용하면 이스케이프 필요한 문자열을 깔끔하게 표현 가능
- 로컬 함수로 중복 제거 가능