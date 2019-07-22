---

layout: default
title: 코틀린 살펴보기
date: 2019-07-20 12:47:00
lastmod : 2019-07-22 23:09:00
sitemap :
  changefreq : daily
  priority : 1.0

---

# 코틀린 기초 문법(\#1)

[코틀린의 장점]({{site.baseurl}}{% post_url 2019-07-15-why-kotlin %})에서 코틀린을 왜 써야 하는지 살펴봤습니다.
이제 코틀린의 문법을 볼 차례입니다.

## Packages, Imports

### Packages

자바와 달린 코틀린에서는 디렉토리와 패키지는 무관합니다.
따라서, 소스코드는 같은 패키지라도 다른 디렉토리에 위치할 수 있습니다.

```kotlin
package my.demo

import java.util.*
```
소스코드 최상단에 package 라는 키워드를 통해 선언합니다.
이렇게 선언된 소스코드의 모든 컨텐츠(class/function 등)는 package 에 속합니다.

```kotlin
package foo.bar

fun baz() { ... }
class Goo { ... }

// ...
```

위 코드에서 선언된 baz 와 Goo 의 풀 네임은 foo.bar.baz, foo.bar.Goo 와 같습니다.

> 패키지를 선언하지 않는다면 이름이 없는 기본 패키지로 정의됩니다.

### Imports

코틀린은 기본적으로 모든 소스파일에 기본적으로 imports 하고 있는 패키지들이 있습니다.
당연히 각 파일에서 추가로 import 할 수 있습니다.

```kotlin
import foo.* // everything in 'foo' becomes accessible
import foo.Bar // Bar is now accessible without qualification
import bar.Bar as bBar // bBar stands for 'bar.Bar'
```

또한 'as' 키워드를 사용하여 충돌 되는 경우에 명시적으로 별명을 부여하여 실수를 줄일 수 있습니다.

> 클래스 이외에도 top-level 프로퍼티나 함수, enum 상수도 import 할 수 있습니다.

## Functions

### Declarations

코틀린에서 함수는 fun 키워드를 사용합니다.
자바와 달리 return 타입을 파라미터 우측에 위치합니다.

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```
첫번째 함수는 파라미터 2개를 받아서 합을 돌려주는 sum 함수입니다.
파라미터의 타입, 리턴 타입등이 자바와 비교하여 우측에 위치한다는 점을 확인하실 수 있습니다.

```kotlin
fun sum(a: Int, b: Int) = a + b
```
첫번째 함수와 동일한데 **함수의 본문이 하나의 표현식이으로 기술이 가능하며 리턴 타입은 타입추론** 으로 생략 가능합니다.

```kotlin
fun printSum(a: Int, b: Int): Unit/** 생략가능 */ {
    println("sum of $a and $b is ${a + b}")
}
```
자바에서 void 키워드는 Unit 클래스로 대체 되며, Unit 은 생략 가능합니다.

#### Default Arguments

함수의 파라미터는 디펄트 값을 지정할 수 있는데, 디펄트 값이 선언된 파라미터는 함수를 호출하려 할때
값을 생략할 수 있습니다. 따라서 불필요한 오버로딩을 줄일 수 있습니다.

```kotlin
fun read(b: Array<Byte>, off: Int = 0, len: Int = b.size) { ... }

fun foo(bar: Int = 0, baz: Int) { ... }

foo(baz = 1) // The default value bar = 0 is used
```

앞에 디펄트 값이 선언된 파라미터(bar)를 생략하고 싶다면 named arguments 를 이용하여
명시적으로 파라미터 이름을 붙여줘야 합니다.

> 만약 bar 가 뒤에 선언되어 있었다면 이름을 붙일 필요가 없습니다.

위에서 잠시 언급된 것처럼 named arguments 를 이용하면 파라미터가 많은 경우 가독성이 높습니다.

#### Explicit return types

표현식이 아닌 블록을 본문으로 하는 함수는 리턴 타입을 명시해야 합니다.
단, Unit 은 생략할 수 있습니다.

> block 인 경우 타입 추론을 하지 않는 이유는 조건문에 따라 다른 타입을 리턴하는 경우가 발생할 수 있고
이는 혼란을 야기할 수 있습니다.

#### Varargs

```kotlin
fun <T> asList(vararg ts: T): List<T> {
    val result = ArrayList<T>()
    for (t in ts) // ts is an Array
        result.add(t)
    return result
}
```

파라미터는 가변인자를 가질 수 있는데 vararg 키워드를 사용합니다.
가변 파라미터는 Array 로 표현 됩니다.

> 하나의 파라미터만 vararg 를 선언할 수 있습니다. 가변인자가 자바와 다르게 순서에 상관 없이 위치할 수 있는데
마지막에 위치해 있는게 아니라면 선언할때 named argument 형태로 호출 되어야 합니다.

```kotlin
val a = arrayOf(1, 2, 3)
val list = asList(-1, 0, *a, 4)
```

위에서 처럼 넘겨주는 인자 중 배열이 존재하는 경우에는 spread 연산자(*) 를 사용하여야 합니다.

#### Infix notation

중위 표현식을 지원합니다. 중위 표현식을 사용하려는 함수의 조건은 아래와 같습니다.

- 멤버 함수이거나 확장 함수여야 한다.
- 단일 파라미터여야 한다.
- 파라미터는 vararg 가 아니여야 하며, default value 가 없어야 한다.

```kotlin
infix fun Int.shl(x: Int): Int { ... }

// calling the function using the infix notation
1 shl 2

// is the same as
1.shl(2)
```
> 중위표현식은 산술 연산보다 우선순위가 낮습니다.

## 변수

현대 언어에서는 Immutable 을 권장합니다. 그 이유는 멀티쓰레드로부터 안전하고,
순수함수(Pure function) 와 함께 부수효과(Side-effect)를 줄이는데 큰 도움을 주기 때문입니다.

### val ***(Immutable)***

val 키워드로 선언된 변수는 단한번만 값을 할당할 수 있고,
그 이후에는 읽기만 가능합니다.
```kotlin
val a: Int = 1 // 선언과 동시에 초기화
val a = 1 // 타입 추론
val b: Int
b = 30 // 선언 후 대입 ( 한번만 가능 )
```

> 정수형 기본 타입은 Int, 소수형 기본 타입은 Double 입니다.

### var ***(Mutable)***

```kotlin
var x = 5
x += 1
```

재할당 가능한 변수는 var 키워드를 사용합니다.


### Using nullable values and checking for null

[billion-dollar mistake](https://en.wikipedia.org/wiki/Tony_Hoare) 10억달러의 실수를 경험하지 않게 하기위해서 명시적으로 nullable 을 표시해야 합니다.

```kotlin
fun parseInt(str: String): Int? {
    // ...
}
```

함수 리턴에 '?' 를 볼 수 있는데, 이것은 함수가 Null 을 반환 할 수 있다고 선언한 것입니다.

따라서 사용하는 곳에서는 반드시 null 을 체크해야 compile error 피할 수 있습니다.

```kotlin
fun printProduct(arg1: String, arg2: String) {
    val x = parseInt(arg1)
    val y = parseInt(arg2)

    // Using `x * y` yields error because they may hold nulls.
    if (x != null && y != null) {
        // x and y are automatically cast to non-nullable after null check
        println(x * y)
    }
    else {
        println("either '$arg1' or '$arg2' is not a number")
    }    
}
```

위에서 보시는 것처럼 x,y 는 null 이 가능하기 때문에 반드시 체크하셔야 합니다.

#### Safe Calls

앞서 살펴보았듯이, kotlin 에서 Nullable 은 명시적으로 선언되어야 하며 이것은 사용할때 반드시 체크되어야 합니다.
따라서 NPE 는 아래 경우에만 발생합니다.

- 명시적으로 throw NullPointerException() 을 한 경우
- !! 연산자를 통해 null이 아님을 개발자가 보장한 경우
- 생성자에서 this 참조를 어딘가에 전달하고 이것이 사용되어졌을 때
- 슈퍼 클래스가 서브 클래스에서 초기화 되지 않은 상태를 사용하는 'open' 멤버를 호출하였을 때
- 자바와 혼용했을 때 발생 가능한 문제

```kotlin
val b: String? = null
println(b?.length)
```

위에서 처럼 '?.' 을 사용하여 if 문 대신 안전하게 호출 할 수 있습니다.

```kotlin
bob?.department?.head?.name
```

safety call 은 체이닝도 가능합니다.

```kotlin
val l: Int = if (b != null) b.length else -1
```
elvis 연산자를 사용하면 위와 같은 코드를 아래와 같이 간결하게 사용할 수 있습니다.

```kotlin
val l = b?.length ?: -1
```

# 마무리

이번 블로그에서는 코틀린 프로그래밍에이써 제일 먼저 숙지해야 할 문법인 패키지의 생성 및 임포트 그리고 함수, 변수에 대해서 알아보았습니다.

다음 블로그에서는 제어문에 대해서 살펴보도록 하겠습니다.
