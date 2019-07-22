---

layout: default
title: 코틀린 살펴보
date: 2019-07-20 12:47:00

---

# 코틀린 기초 문법

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



## 변수

## 분기

## 반복
