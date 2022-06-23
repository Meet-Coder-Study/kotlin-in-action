# 안녕하세요

## 2.1 기본요소: 함수와 변수

**함수를 작성하는 방법**

![1](https://user-images.githubusercontent.com/26001202/175311473-b45c5b88-bdf0-4ff0-a965-71885779af9f.png)

- 함수의 선언은 fun 키워드로 시작
- 함수 이름 뒤에는 괄호 안에 파라미터 목
- 함수의 반환 타입은 파라미터 목록의 닫는 괄호 다음에 위치, 단 괄호와 반환 타입 사이를 콜론으로 구분

> **함수의 기본 구조**
> 
- 코틀린의 if는 문장이 아니고 결과를 만드는 식(expression)
- 문(statement)와 식(expression)의 구분
    - 코틀린에서는 If는 식이지 문이 아니다.
    - 식은 값을 만들어 내며, 다른 식의 하위 요소로 계산에 참여 가능
    - 대입문은 자바에서 `식`이었으나 코틀린에서는 `문`의 차이가 있다.
    - `**식이 본문인 함수**`
        - 본문이 중괄호로 둘러싸인 함수를 블록이 본문인 함수
        - 등호와 식으로 이뤄진 함수를 식이 본문인 함수
        - `**반환 타입을 생략할 수 있는 이유 ?**`
            - 코틀린은 정적 타입 지정 언어로 모든 변수나 모든 식에는 타입이 있으며, 모든 함수는 반환 타입이 정해져야 한다.
                
![2](https://user-images.githubusercontent.com/26001202/175311495-e0b4480a-6839-41aa-bbea-762c086e59a2.png)


            - 식이 본문인 함수의 경우 사용자가 반환 타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해서 식의 결과 타입을 함수 반환 타입으로 정해준다.
                ![3](https://user-images.githubusercontent.com/26001202/175311534-6719665e-f246-4863-b43e-026c4f56e5fc.png)

                
                
            - 타입 추론(Type Inference): 컴파일러가 타입을 분석해 프로그래머 대신 프로그램 구성 요소의 타입을 정해주는 기능
        - 식이 본문인 함수의 반환 타입만 생략 가능
            - 블록이 본문인 함수가 값을 반환한다면 반드시 반환 타입을 지정, return 문을 사용해 반환 값을 명시

> **변수**
> 
- 코틀린에서 변수의 선언
    - 변수 이름 뒤에 타입을 명시하거나 생략을 허용
    - 식이 본문인 함수에서와 마찬가지로 여러분이 타입을 지정하지 않으면 컴파일러가 초기화 식을 분석해서 초기화 식의 타입을 변수 타입으로 지정
    - 초기화 식을 사용하지 않고 변수를 선언하려면 변수 타입을 반드시 명시!!
    - 초기화 식이 없다면 변수에 저장될 값에 대해 아무 정보가 없기 때문에 컴파일러가 타입을 추론할 수 없다. 이러한 경우 타입을 반드시 지정해야 함
- 변경 가능한 변수와 변경 불가능한 변수
    - val: 변경 불가능한 참조를 저장하는 변수
        - val로 선언된 변수는 일단 추기화하고 나면 재대입 불가능
        - 자바의 final 키워드
    - var: 변경 가능한 참조를 저장하는 변수
        - 변수의 값은 언제든 변경 가능
        - 자바의 일반 변수
    - 주의사항
        - 변수의 값을 변경이 가능하지만, 변수의 타입은 고정되어 바뀌지 않음
        (가변의 뜻이 타입 자체는 아님)
            
            ![4](https://user-images.githubusercontent.com/26001202/175311558-547fbd50-7ec8-4b94-b29b-3868f20f2d59.png)

            
        - 개발 방식 순서
            - val 키워드를 사용해 불변 변수를 선언, 추후 필요한 경우 var로 변경
            - 변경 불가능한 참조와 변경 불가능한 객체를 부수효과가 없느 함수와 조합해 사용하면 함수형 코드에 가까워진다.

> **문자열 템플릿**
> 
- 문자열 템플릿이란?
    - 스크립트 언어와 비슷하게 변수를 문자열 안에 사용하는 방법
        
        ![5](https://user-images.githubusercontent.com/26001202/175311588-5e2efe3c-3901-49e4-b61f-170f927adbe3.png)

        
    - 컴파일러는 각 식을 정적(컴파일 시점)으로 검사하기 때문에 존재하지 않는 변수를 문자열 템플릿 안에서 사용하는 경우 컴파일 오류가 발생할 수 있다.
- 문자열 템플릿의 주의사항
    - 문자열 템플릿 안에서 변수 이름만 사용하는 경우라도 중괄호로 변수명을 감싸는 습관

## 2.2 클래스와 프로퍼티

> 프로퍼티
> 
- 클래스의 기본 목적은 데이터를 캡슐화하기 위함
    - 멤버 필드의 가시성은 보통 private
    - 클라이언트가 데이터에 접근하기 위한 방법으로 접근자 메서드(accessor method)를 제공
    - 일반적으로 필드를 읽기 위한 getter 제공, 필드를 변경이 필요한 경우 setter 제공
- 자바에서 프로퍼티의 정의
    - 필드와 getter를 한데 묶어 프로퍼티라 부름
- 코틀린에서 프로퍼티 정의
    - 자바의 필드와 getter를 완전하게 대신하여 정의
        
        ![6](https://user-images.githubusercontent.com/26001202/175311609-c05ddb73-0399-4939-8f8e-65a013f340ca.png)

        
- Backing Field: 뒷받침하는 필드 ⭐️⭐️
    - 프로퍼티에는 그 프로퍼티의 값을 저장하기 위한 필드가 존재
    - 객체에 어떠한 로직처리가 이루어진 필드
    - 커스텀 게더를 통해 구현 가능

> 커스텀 접근자
> 
- 프로퍼티의 접근자를 사용자 정의로 작성하는 방식
    
   ![7](https://user-images.githubusercontent.com/26001202/175311637-574bdd57-0864-4c5c-aef3-743cae692d0d.png)

    
- 코틀린 소스코드 구조: 디렉토리와 패키지
    - 자바 학습 할 때와 같은 개념
    - 기존 자바와 코틀린의 다른점이라고 하면 최상위에 정의된 함수에 대한 개념이 추가 되었다는 것
    - 최상위에 정의된 함수로 선언된 함수들은 동일한 패키지내에서 import 없이 접근이 가능
    - 하지만 최종적으로 자바와의 상호운용성이라는 측면에서 봤을때 자바에서 사용하던 방식으로 패키지를 구성해야 함

## 2.3 선택표현과 처리: enum과 when

> enum 클래스 정의
> 

![8](https://user-images.githubusercontent.com/26001202/175311662-5d4c8bbd-1361-46b8-a937-e905039eba27.png)


- 소프트 키워드: enum ⭐️⭐️
    - class 앞에서는 enum 클래스를 의미, class가 없다면 키워드가 아님
    - enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언
- 키워드: class ⭐️⭐️

> 프로퍼티와 메서드가 있는 enum 클래스 선언
> 

![9](https://user-images.githubusercontent.com/26001202/175311680-6ba60542-10a5-4cbc-8a9b-ae1b7b874047.png)


> when으로 enum 클래스 다루기
> 
- if와 마찬가지로 when도 값을 만들어내는 식이기 때문에 식이 본문인 함수에 when을 사용가능

![10](https://user-images.githubusercontent.com/26001202/175311696-8c29d023-0aee-4aee-a9df-119109b56bba.png)



- 한 분기 안에서 여러 값을 매치 패턴으로 사용할 수 있다. 그럴 경우 값 사이를 콤마로 분리

![11](https://user-images.githubusercontent.com/26001202/175311710-76cbabcf-4251-4dc3-a4a0-9a6d2b699991.png)



- 상수 값을 임포트하여 enum 클래스 수식자 없이 사용 가능

![12](https://user-images.githubusercontent.com/26001202/175311728-6b7e3bf1-7679-4e60-ac98-e2200db9283f.png)



- when의 분기 조건에 여러 다른 객체 사용
    - 객체 사이를 매치할 때 동등성을 사용
    - when 식은 인자 값과 매치하는 조건 값을 찾을 때까지 각 분기를 검사

![13](https://user-images.githubusercontent.com/26001202/175311754-8517a779-fbcf-4ac1-961f-b4a05622c1eb.png)



> when과 의미의 객체를 함께 사용
> 
- 코틀린 when의 분기 조건은 임의의 객체를 허용

![14](https://user-images.githubusercontent.com/26001202/175311779-250b84ef-e730-4031-af36-1ad6d0c5d21b.png)



> 인자 없는 when 사용
> 
- set 인스턴스를 생성하는 불필요한 비용을 줄이기 위해서 인자가 없는 when 식을 사용할 수 있다.

![15](https://user-images.githubusercontent.com/26001202/175311799-d4548919-fb7f-456a-8efc-7deb8646dfcc.png)


> 스마트 캐스트
> 
- Expr 인터페이스에는 두 가지 구현 클래스가 존재
    - 식을 평가하려면 두 가지 경우를 고려해야 한다.
    - 어떤 식이 수라면 그 값을 반환
    - 어떤 식의 합계라면 좌항과 우항의 값을 계산 한 뒤 그 두 값을 합한 값을 반환한다.

![16](https://user-images.githubusercontent.com/26001202/175311824-14db5e81-14a1-4f1c-96b4-a0b6905700dc.png)


![17](https://user-images.githubusercontent.com/26001202/175311858-d37e9832-de10-4340-bb56-be8c1e9c95b4.png)



![18](https://user-images.githubusercontent.com/26001202/175311885-cc8d156e-4a26-4984-9da1-c389405740ac.png)


![19](https://user-images.githubusercontent.com/26001202/175311902-4582d402-4d04-4742-9eee-499b21789518.png)





> 리펙토링: if를 when 으로 변경
> 

![20](https://user-images.githubusercontent.com/26001202/175311940-4d9f9129-a760-4046-b024-432cd568861f.png)



> if 중첩 대신 when 사용하기
> 


![21](https://user-images.githubusercontent.com/26001202/175311956-60e13242-9826-4d8e-b74b-21c396fbfb65.png)


> if와 when의 분기에서 블록 사용
> 
- 함수에 로그를 추가하고 싶은 경우 각 분기를 블록으로 만들고 블록의 맨 마지막에 그 분기의 결과 값을 위치

![22](https://user-images.githubusercontent.com/26001202/175311987-e3ba23c0-7ca6-4c82-a395-7c138f80f037.png)



<br>


## 2.4 대상을 이터레이션: while 과 for 루프

기본적인 반복문은 자바와 크게 다르지 않다. while, do-while 다 있다.

- ( **..** ) rangeTo 연산자가 존재한다. `val oneToTen = 1..10   // 1.rangeTo(10)`
- 이터레이션 반복문, `for in` 문에서 다양한 응용이 가능하다.

```kotlin
for (i in 1..100) {
   /* 1 2 3 ... 100 */
}

// downTo, step, until 같은 확장함수를 사용하면 더 재밌어진다.
for (i in 100 downTo 1 step 2) { // 100 부터 1까지 +(2)씩
   /* 100 98 96 ... 2 */
}
```

참고로 downTo 와 step 메서드는 기본 패키지인 `kotlin.range` 에 아래와 같이 정의되어있다.

```kotlin
public infix fun Int.downTo(to: Int): IntProgression {
    return IntProgression.fromClosedRange(this, to, -1)
}

public infix fun IntProgression.step(step: Int): IntProgression {
    checkStepIsPositive(step > 0, step)
    return IntProgression.fromClosedRange(first, last, if (this.step > 0) step else -step)
}
```

<br>


### 2.4.3 맵에 대한 이터레이션

코틀린에서는 get, set 뿐만 아니라 아래와 같이 map 자료형도 간결하게 사용할 수 있다.

```kotlin
myMap.put(c, binary)
myMap[c] = binary
```

반복문도 이와 비슷하게 객체 분해를 사용해서 map 같은 자료형을 간결하게 쓸 수 있다.

```kotlin
for (( letter, binary ) in binaryReps) { // (key, value)
    println("$letter = $binary")
}
```


<br>

## 2.5 코틀린의 예외 처리

기본적인 사용방법은 자바와 크게 다를 건 없다.

- Checked 예외를 반드시 처리할 필요없다. catch 하지 않더라도 코틀린 컴파일러에서 무시된다.
`* Checked 예외는 RuntimeException을 상속받지 않은 다른 예외를 의미합니다. 자바에서는 반드시 처리하거나 method throws로 표시해야하죠.`
- try, throw 같은 키워드가 식(Expression)으로 취급된다.

```kotlin
fun method() {
	val number = try{ // try - catch 표현식으로 마지막 값을 반환할 수 있다. 
	    Integer.parseInt(reader.readLine()) 
	} catch (e: NumberFormatException {
	     return // 여기서 리턴은 메서드를 종료한다.
	}
}
```
* 코틀린 예외는 변환 하는게 아닙니다. 그냥 체크 예외가 없는거에요. (컴파일러가 무시)
* @Throws 는 자바와 함께 사용할 때 유용합니다. 또는 확실하게 예외를 catch 하라고 표시하거나요 : 참조 https://www.baeldung.com/kotlin/throws-annotation

<br>


## 2.6 요약

- 문자열 템플릿을 사용하면 깔끔하다. `$` 와 `"""`
- 코틀린에서는 DTO나 값 객체를 `data` 를 사용해서 간결하게 표시할 수 있다.
- 코틀린에서 if, try, when 등은 식(Expression)으로 사용해서 값을 반환할 수 있다.
- 코틀린에서는 타입을 검사하고나면 컴파일러가 스마트 캐스팅을 해주기에, 자동으로 타입을 변경해준다.
- 반복문과 함께 `in 1..5` 등을 사용하면 코드를 간결하게 만들 수 있다.
- 코틀린의 예외처리는 자바와 비슷하나, Checked 예외를 꼭 처리할 필요는 없다. 코틀린 컴파일러에 의해 무시 된다.

