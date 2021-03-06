# Chapter 5. 람다로 프로그래밍

> **스터디 날짜**
>> 2018-10-24(수)<br/>2018-10-31(수)<br/>2018-11-07(수)

- **람다식**<sup>lambda expression</sup> 또는 **람다**는 다른 함수에 넘길 수 있는 작은 코드 조각을 뜻한다.

## 5.1 람다식과 멤버 참조

### 5.1.1 람다 소개: 코드 블록을 함수 인자로 넘기기

- 자바 8 이전에는 익명 클래스를 통해 람다와 비슷한 동작을 구현하였다. 

```java
/* 자바 */
/* 익명 클래스로 리스너 구현하기 */
button.setOnClickListener(new onClickListener() {
    @Override
    public void onClick(View view) {
        /* 클릭 시 수행할 동작 */
    }
});
```

- 위 코드를 코틀린의 람다를 사용하면 아래와 같은 코드가 된다.

```kotlin
/* 코틀린 */
/* 람다로 리스너 구현하기 */
button.setOnClickListener { /* 클릭 시 수행할 동작 */ }
```

### 5.1.2 람다와 컬렉션

```kotlin
/* 사람의 이름과 나이를 저장하는 Person 클래스 */

data class Person(val name: String, val age: Int)
```

- 기존 방식으로 루프를 통해 가장 나이가 많은 사람을 구하는 방법은 아래와 같다.

```kotlin
fun findTheOldest(people: List<Person>) {
    var maxAge = 0
    var theOldest: Person? = null
    for (person in people) {
        if (person.age > maxAge) {
            maxAge = person.age
            theOldest = person
        }
    }
    
    println(theOldest)
}

>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> findTheOldest(people)
Person(name=Bob, age=31)
```

- 코틀린의 라이브러리 함수를 사용하면 아래와 같이 작성할 수 있다.

```kotlin
/* 코틀린이 지원하는 람다를 사용해 컬렉션 검색하기 */
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.maxBy { it.age })
Person(name=Bob, age=31)
```

- 위 코드를 멤버 참조를 이용해서 작성할 수도 있다.

```kotlin
/* 멤버 참조를 사용해 컬렉션 검색하기 */
people.maxBy(Person::age)
```

### 5.1.3 람다 식의 문법

```kotlin
/* 람다 식 문법 */
{ x: Int, y: Int -> x + y }
```

- 람다는 항상 중괄호 사이에 위치한다.
- 화살표(-&gt;))가 인자 목록과 람다 본문을 구분해준다.
- 람다 식은 변수에 저장할 수 있다.
- 람다 식을 직접 호출할 수도 있다.

```kotlin
/* 람다 식을 변수에 저장하기 */
>>> val sum = { x: Int, y: Int -> x + y }
>>> print(sun(1, 2))
3

/* 람다 식을 직접 호출하기 */
>>> { println(42) } ()
42
```

- 앞서 보았던 예제에 대해서 정식으로 람다를 작성해보면 아래와 같다.

```kotlin
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.maxBy { it.age })
Person(name=Bob, age=31)
```

```kotlin
/* 정식으로 람다를 사용하여 구현 */
people.maxBy({p: Person -> p.age})
```

- 람다는 인자가 단 하나뿐인 경우 굳이 인자에 이름을 붙이지 않아도 된다.
- 함수 호출 인자의 제일 마지막이 람다 식이라면, 그 람다를 괄호 밖을 빼내는 문법 관습이 존재한다.

```kotlin
people.maxBy() { p: Person -> p.age }
```

- 위 코드처럼 함수의 인자가 1개이고, 람다라면 괄호를 생략할 수 있다.

```kotlin
people.maxBy { p: Person -> p.age }
```

- 챕터 3에서 보았던 joinToString 예제를 다시 살펴보자.

```kotlin
/* 이름 붙인 인자를 사용해 람다 넘기기 */
>>> val people = listOf(Person("이몽룡", 29), Person("성춘향", 31))
>>> val names = people.joinToString(separator = " ", transform = { p: Person -> p.name })
>>> println(names)
이몽룡 성춘향
```

- 위 예제에서 joinToString 함수의 마지막 인자가 람다이므로 괄호 밖을 뺄수 있다.

```kotlin
people.joinToString(separator = " ") { p: Person -> p.name } 
```

- 로컬 변수처럼, 람다 또한 타입 추론이 가능하다.

```kotlin
/* 람다의 타입 추론 */
people.maxBy { p: Person -> p.age }  /* <-- 파라미터 타입 명시 */
people.maxBy { p -> p.age }  /* <-- 컴파일러가 타입을 추론 */
```

- 람다의 파라미터가 1개이고, 그 타입을 컴파일러가 추론할 수 있는 경우 `it`이라는 키워드를 사용할 수 있다.

```kotlin
/* 디폴트 파라미터 이름 it 사용하기 */
people.maxBy { it.age }
```

- 람다를 변수에 저장할 때에는 파라미터 타입을 추론할 문맥이 존재하지 않으므로, 반드시 타입을 명시해야 한다.

```kotlin
>>> val getAge = { p: Person -> p.age }
>>> people.maxBy(getAge)
```

- 본문이 여러 줄로 이루어진 람다는 제일 마지막에 있는 식이 람다의 결과 값이 된다.

```kotlin
val sum = { x: Int, y: Int ->
                println("Computing the sum of ${x} and ${y}...")
                x + y  /* <-- 결과 값이 되는 식 */
}

>>> println(sum(1, 2,))
Computing the sum of 1 and 2...
3
```

### 5.1.4 현재 영역에 있는 변수에 접근

- 람다를 함수 안에서 정의하면 함수의 인자뿐 아니라, 람다 정의 앞에 선언된 로컬 변수까지 접근 가능하다. 아래 예를 보자.

```kotlin
/* 함수 인자를 람다 안에서 사용하기 */
fun printMessageWithPrefix(messages: Collection<String>, prefix: String) {
    messages.forEach {
        println("${prefix} ${it}")  /* <-- 람다 안에서 함수의 "prefix" 인자에 접근 가능하다. */
    }
}

>>> val errors = listOf("403 Forbidden", "404 Not Found")
>>> printMessageWithPrefix(errors, "Error: ")
Error: 403 Forbidden
Error: 404 Not Found
```

- 코틀린 람다와 자바의 중요한 차이점 하나는, 코틀린 람다는 변수가 final이 아니어도 접근이 가능하다는 점이다.
- 코틀린 람다는, 람다의 바깥에 있는 변수를 변경할 수도 있다.

```kotlin
/* 람다 안에서 바깥에 있는 로컬 변수 변경하기 */
fun printProblemCounts(responses: Collection<String>) {
    var clientErrors = 0
    var serverErrors = 0
    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++  /* <-- 람다 안에서 람다 밖의 변수를 변경 */
        } else if (it.startsWith("5")) {
            serverErrors++  /* <-- 람다 안에서 람다 밖의 변수를 변경 */
        }
    }
    
    println("${clientErrors} client errors, ${serverErrors} server errors")
}

>>> val responses = listOf("200 OK", "418 I'm teapot", "500 Internal Server Error")
>>> printProblemCounts(responses)
1 client errors, 1 server errors
```

- 앞서 본 예제들의 prefix, clientErrors, serverErrors와 같이 람다 안에서 참조되는 외부 변수를 `람다가 캡쳐`<sup>capture</sup>`한 변수`라고 부른다.
- 캡쳐한 변수가 있는 람다를 저장하면, 캡쳐한 변수가 있는 함수가 종료되더라도 람다에서는 캡쳐한 변수를 읽거나 쓸 수 있다.
- final 변수를 캡쳐한 경우에는 람다 코드를 변수 값과 함께 저장한다.
- final이 아닌 변수를 캡쳐한 경우에는, 변수를 특별한 래퍼로 감싼 다음, 래퍼의 참조와 함께 람다 코드를 저장한다.
- 자바의 경우에는 final 변수만 캡쳐할 수 있다.

### 5.1.5 멤버 참조

```kotlin
val getAge = Person::age
``` 

- ::를 사용하는 식을 **멤버 참조**<sup>member reference</sup>라고 부른다.
- 아래 3개는 모두 같은 결과를 반환한다.

```kotlin
people.maxBy(Person::age)
people.maxBy { p -> p.age }
people.maxBy { it.age }
```

- 최상위에 선언된 함수나 프로퍼티를 멤버 참조로 사용할 수도 있다.

```kotlin
fun salute() = println("Salute!")
>>> run(::salute)
Salute!
```

## 5.2 컬렉션 함수형 API

### 5.2.1 필수적인 함수: filter와 map

- `filter`와 `map`은 컬렉션을 활용할 때 기반이 되는 함수이다.
- `filter` 함수는 컬렉션을 이터레이션하면서 인자로 넘긴 람다 식이 true를 반환하는 원소만 모아준다.

```kotlin
>>> val list = listOf(1, 2, 3, 4)
>>> println(list.filter { it % 2 == 0 })
[2, 4]
```
```kotlin
/* 30살 이상인 사람만 filter 하는 예 */
data class Person(val name: String, val age: Int)

>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.filter { it.age > 30 })
[Person(name=Bob, age=31)]
```

- 컬렉션의 원소를 변환하려면 map 함수를 사용한다.
- map 함수는 인자로 넘긴 람다를 적용한 결과를 모아 새로운 컬렉션을 만든다.
- 숫자 리스트에서 각 숫자의 제곱한 리스트를 얻는 예는 아래와 같다.

```kotlin
>>> val list = listOf(1, 2, 3, 4)
>>> println(list.map { it * it })
[1, 4, 9, 16]
```

- 인적사항을 저장하는 리스트에서 사람의 이름만 리스트로 뽑아내려면 아래와 같이 map을 사용하면 된다.

```kotlin
>>> val people = listOf(Person("Alice", 29), Person("Bob", 31))
>>> println(people.map { it.name })
[Alice, Bob]
```

- 위 코드를 아래와 같이 줄일 수도 있다.

```kotlin
people.map(Person::name)
```

- 30살 이상인 사람의 이름을 출력하는 방법은 아래와 같다.

```kotlin
>>> people.filter { it.age > 30 }.map(Person::name)
[Bob]
```

- 리스트가 아닌 자료형 맵 에 대해서도 filter와 map 함수를 적용할 수 있다.

```kotlin
>>> val numbers = mapOf(0 to "zero", 1 to "one")
>>> println(numbers.mapValues { it.value.toUpperCase() })
[0=ZERO, 1=ONE]
```

- 맵의 경우는 Key와 Value를 처리하는 함수가 각각 존재하며, `filterKeys, makKeys`와 `filterValues, mapValues`를 사용한다.

### 5.2.2 all, any, count, find: 컬렉션에 Predicate 적용

- `all`과 `any`는 컬렉션의 모든 원소에 대해 주어진 람다식울 만족하는지 판단하는 연산이다.
- `count` 함수는 주어진 람다의 조건을 만족하는 원소의 개수를 반환한다.
- `find` 함수는 주어진 `람다의 조건을 만족하는 첫 번째 원소`를 반환한다.
- 나이가 27살 이하인지 판단하는 Predicate 함수 canBeInClub27 예제를 보자.

```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }

/* all 함수를 사용하여 모든 원소가 이 Predicate를 만족하는지 확인 */
>>> val people = listOf(Person("Alice", 27), Person("Bob", 31))
>>> println(people.all(canBeInClub27))
false

/* any 함수를 사용하여 하나라도 만족하는 원소가 있는지 확인 */
>>> println(people.any(canBeInClub27))
true

/* count 함수를 사용하여 Predicate를 만족하는 원소의 개수를 확인 */
>>> println(people.count(canBeInClub27))
1

/* find 함수를 사용하여 조건을 만족하는 첫번째 원소 확인 */
>>> println(people.find(canBeInClub27))
Person(name=Alice, age=27)
```

- `find` 함수의 경우 조건을 만족하는 원소가 없는 경우 `null`을 반환한다.
- `firstOrNull` 함수를 사용하면, 조건을 만족하는 원소가 없으면 null을 반환한다는 사실을 좀 더 명확하게 표현할 수 있다.

### 5.2.3 groupBy: 리스트를 여러 그룹으로 이루어진 맵으로 변경

- `groupBy` 함수를 사용하면 모든 원소에 대해 특성에 따라 여러 그룹으로 나눌 수 있다.

```kotlin
>>> val people = listOf(Person("Alice", 27), Person("Bob", 31), Person("Carol", 31))
>>> println(people.groupBy { it.age })
{27=[Person(name=Alice, age=27)], 31=[Person(name=Bob, age=31), Person(name=Carol, age=31)]}
```

- 첫 글자에 따라 분류하는 예제는 아래와 같다.

```kotlin
>>> val list = listOf("a", "ab", "b")
>>> println(list.groupBy(String::first))
{a=[a, ab], b=[b]}
```

### 5.2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

- Book 클래스를 사용하여 책에 대한 정보를 저장하는 도서관이 있다고 가정하자. 책마다 저자가 한 명 또는 여려 명일 수 있다. 책의 저자를 모두 모은 집합을 다음과 같이 가져올 수 있다.

```kotlin
class Book(val title: String, val authors: List<String>)

books.flatMap { it.authors }.toSet()
```

- flatMap 함수는 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고, 그 결과로 얻어지는 여러 리스트를 하나의 리스트에 모은다.

```kotlin
>>> val strings = listOf("abc", "def")
>>> println(strings.flatMap { it.toList() })
[a, b, c, d, e, f]
```

- flatmap 함수를 사용하여 저자 목록을 가져오는 예제를 보자.

```kotlin
val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
                Book("Mort", listOf("Terry Pratchett")),
                Book("Good Omens", listOf("Terry Pratchett", "Neil Gaiman")))
>>> println(books.flatMap { it.authors }.toSet())
[Jasper Fforde, Terry Pratchett, Neil Gaiman]
```

- 리스트 안에 리스트가 존재하고 특별한 람다 조건을 사용하지 않고 하나의 리스트로 만드려면 `flatten` 함수를 쓰면 된다.

## 5.3 지연 계산(lazy) 컬렉션 연산

- 컬렉션 함수인 map, filter 등은 결과 컬렉션을 즉시 생성한다. 이는 컬렉션 함수 호출 매 단계마다 계산 중간 결과를 새로운 컬렉션을 생성하여 담는다는 의미이다.
- **시퀀스**<sup>sequence</sup>를 사용하면 계산 결과를 임시로 담는 컬렉션을 생성하지 않는다.

```kotlin
people.map(Person::name).filter { it.startsWith("A") }
```

- 위 코드는 map과 filter에 의해서 임시 리스트를 2개를 만들게 된다.
- 원소가 수 백만개가 되면, 임시 리스트가 매우 많이 만들어지므로, 시퀀스를 사용하는 것이 효율적이다.

```kotlin
people.asSequence()  /* <-- 시퀀스로 변환 */
    .map(Person::name)
    .filter { it.startsWith("A") }
    .toList()
```

- 위 코드는 map과 filter에 의해 발생하는 임시 컬렉션이 생성되지 않기 때문에 `원소가 많은 경우 성능이 눈에 띄게 좋아진다.`
- 시퀀스를 사용하면 iterator라는 메소드를 통해 시퀀스로부터 원소 값을 얻을 수 있다.
- 시퀀스 인터페이스의 장점은 계산이 필요할 때만 수행 된다는 점이다.
- 시퀀스를 다시 리스트로 만드는 경우 toList 함수를 사용한다.
- 시퀀스가 무조건 좋다면 코틀린에서 굳이 명시적으로 시퀀스와 컬렉션을 분리시키지 않았을 것이다. 각 원소를 index를 사용하여 순차적이지 않는 방법으로 접근하는 하는 경우 시퀀스를 사용할 수 없고 컬렉션으로 변환해야 한다.

### 5.3.1 시퀀스 연산 실행: 중간 연산과 최정 연산

- 시퀀스의 연산은 중간<sup>intermediate</sup>연산과 최종<sup>terminal</sup>연산으로 나뉜다.
- 최종 연산이 없는 예제는 아래와 같다.

```kotlin
listOf(1, 2, 3, 4).asSequence()
    .map { print("msp(${it}) "); it * it }
    .filter { print("filter(${it}) "); it % 2 == 0 }
    
/* 출력 */
          <-- 아무런 출력 없음
```

- 위 예제에 toList()를 추가하면 아래와 같다.

```kotlin
listOf(1, 2, 3, 4).asSequence()
    .map { print("map(${it}) "); it * it }
    .filter { print("filter(${it}) "); it % 2 == 0 }
    .toList()
    
/* 출력 */
map(1) filter(1) map(2) filter(4) map(3) filter(9) map(4) filter(16)
```

- 시퀀스는 toList()를 호출하면 비로소 모든 계산이 수행 된다.
- 시퀀스의 모든 연산은 각 원소에 대해 순차적으로 적용된다.
- 시퀀스는 각 원소에 연산을 차례대로 적용하다가 조건에 만족하는 결과를 얻게되면 이후에 원소가 존재하더라도 더 이상 연산이 발생하지 않고 끝이 난다. 아래 예를 보자.

```kotlin
>>> println(listOf(1, 2, 3, 4).asSequence()
       .map { println("map(${it*it}) "); it * it }
       .find { it > 3 })

/* 출력 결과 */
map(1)
map(4)  /* <-- 두번째 원소인 2의 제곱이 4이므로 it > 3 조건을 만족한다. 시퀀스이므로 2 다음에 있는 3, 4는 연산되지 않는다. */ 
4
```

- map과 filter 함수를 서로 다른 순서로 호출하여도 최종 결과는 같다. 하지만 연산을 수행하는 횟수는 다르다. 따라서 호출 순서를 신경 쓰면 최적화를 할 수 있다.

### 5.3.2 시퀀스 만들기

- 시퀀스를 만드는 방법은 2가지이다. 컬렉션.asSequence() 를 사용하거나, generateSequence 함수를 사용하는 것이다.
- 아래 코드는 generateSequence 함수를 사용하여 0~100까지 자연수 합을 구하는 예제이다.

```kotlin
/* 자연수의 시퀀스를 생성하고 사용하기 */
>>> val naturalNumbers = generateSequence(0) { it + 1}
>>> val numberTo100 = naturalNumbers.takeWhile { it <= 100 }
>>> println(numbersTo100.sum())  /* <-- 위 sum() 함수를 실행할 때 모든 연산이 수행 된다. */ 
5050
```

### 5.4 자바 함수형 인터페이스 활용

- 코틀린 람다는 자바 API에 사용해도 아무 문제가 없다.
 
```java
/* 자바 */
/* 자바8 이전에 사용하던 익명 클래스 예제 */
public interface OnClickListener {
    void onClick(View v);
}

public class Button {
    public void setOnClickListener(OnClickListener l) {
        ...
    }
}

button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View v) {
        ...
    }
})
```

- 코틀린에서는 익명 클래스 대신 람다를 넘길 수 있다.

```kotlin
button.setOnClickListener { view -> ... }
```

- OnClickListener 인터페이스처럼 추상 메소드가 단 하나만 있는 인터페이스를 **함수형 인터페이스**<sup>funtional interface</sup> 또는 **SAM 인터페이스**라고 한다.
- **SAM**은 **단일 추상 메소드**<sup>single abstract method</sup>라는 뜻이다.

### 5.4.1 자바 메소드에 람다를 인자로 전달

- 자바 메소드에 코틀린 람다를 전달할 수 있다.

```java
/* 자바 */
void postponeComputation(int delay, Runnable computation);
```

- 위 자바 코드에 코틀린 람다를 넘길 수 있다. 컴파일러는 자동으로 람다를 Runnable 인스턴스로 변환해준다. 여기서 `Runnbale 인스턴스`라는 말은 실제로는 `Runnable을 구현한 익명 클래스의 인스턴스`라는 뜻이다.

```kotlin
postponeComputation(1000) { println(42) }
```

- Runnable을 구현하는 익명 클래스를 명시적으로 만들어 사용할 수 있다.

```kotlin
postponeComputation(1000, object : Runnable {
    override fun run() {
        println(42)
    }
})
```

- 람다와 익명 객체는 차이가 있다. 익명 객체를 명시적으로 선언하는 경우 메소드를 호출할 때마다 새로운 익명 객체가 생성된다. 람다를 사용하면 컴파일러에 의해 자동 생성된 익명 객체가 단 하나 만들어지고, 해당 익명 객체를 재상용한다. 

### 5.4.2 SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경

- SAM 생성자는 `람다`를 `함수형 인터페이스의 인스턴스`로 변환할 수 있게 컴파일러가 자동으로 생성한 함수이다. 만약 컴파일러가 자동으로 `함수형 인터페이스 무명 클래스`로 바꾸지 못 하는 경우 SAM 생성자를 사용할 수 있다.

```kotlin
/* SAM 생성자를 사용하 값 반환하기 */
fun createAllDoneRunnable() : Runnbale {
    return Runnable { println("All Done") }
}

>>> createAllDoneRunnable().run()
All Done!
```

- SAM 생성자의 이름은 사용하려는 함수형 인터페이스의 일므과 같다.
- 람다로 생성한 `함수형 인터페이스 인스턴스`를 변수에 저장하는 경우에도 SAM 생성자를 사용할 수 있다.

```kotlin
/* SAM 생성자를 사용해 listener 인스턴스 재사용하기 */
val listener = OnClickListener { view -> 
    val text = when (view.id) {
        R.id.button1 -> "First Button"
        R.id.button2 -> "Second Button"
        else -> "Unknown button"
    }
    toast(text)  /* <-- "text"의 값을 사용자에게 출력 */
}

button1.setOnClickListener(listener)
button2.setOnClickListener(listener)
```

## 5.5 수신 객체 지정 람다: with와 apply

### 5.5.1 with 함수

- `with` 라는 라이브러리 함수를 사용하면 어떤 객체의 이름을 반복하지 않고 연산을 수행 할 수 있다.

```kotlin
/* with 없이 알파벳 만들기 */
fun alphabet() : String {
    val result = StringBuilder()
    for (letter in 'A'..'Z') {
        result.append(letter)
    }
    
    result.append("\nNow I know the alphabet!")
    return result.toString()
}

>>> println(alphabet())
ABCDEFGHIJKLMNOPQRSTUVWXYZ
Now I know the alphabet!
```

- 위 예제를 `with`로 다시 작성하면 아래와 같다.

```kotlin
/* with를 사용해 알파벳 만들기 */
fun alphabet() : String {
    val result = StringBuilder()
    return with(result) {
        for (letter in 'A'..'Z') {
            this.append(letter)
        }
        
        this.append("\nNow I know the alphabet!")
        this.toString()
    }
}
```

- 위 예제를 result 변수를 없애어 한 번 더 리팩토링 할 수 있다.

```kotlin
/* with와 식을 본문으로 하는 함수를 활용해 알파벳 만들기 */
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    
    append("\nNow I know the alphabet!")
    toString()
}
```

### 5.5.2 apply 함수

- `apply` 함수는 거의 with와 같다. 유일한 차이점은 apply는 항상 자신에게 전달된 객체를 반환한다는 점이다.

```kotlin
/* apply를 사용해 알파벳 만들기 */
fun alphabet() = StringBuilder().apply {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}.toString()
```

- apply 함수는 객체의 인스턴스를 만듬과 동시에 프로퍼티 중 일부를 초기화 하는 경우 유용하다. 자바에서는 별도의 Builder 객체가 이런 영할을 담당한다.
- apply를 객체의 초기화에 활용하는 예는 아래와 같다.

```kotlin
/* apply를 TextView 초기화에 사용하기 */
fun createViewWithCustomAttributtes(context: Context) =
    TextView(context).apply {
        text = "Sample Text"
        textSize = 2.0
        setPadding(10, 0, 0, 0)
    }
```

- 표준 라이브러리의 `buildString` 함수를 사용하면 alphabet 함수를 더 단순화 할 수 있다.

```kotlin
/* buildString으로 알파벳 만들기 */
fun alphabet() = buildString {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}
```
