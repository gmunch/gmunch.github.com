---

layout: default
title: 왜 코틀린인가?
date: 2019-07-15 21:05:00

---

# 왜 코틀린을 써야 하는가?

먼저, 왜 코틀린을 왜 써야하는지 개인적인 생각을 적기 이전에 코틀린 공식 사이트에서 이야기 하는 코틀린을 왜 써야 하는가?에 대해서 알아보자.

![](https://raw.githubusercontent.com/gmunch/gmunch.github.com/master/assets/images/kotlinadvantage.png)

[kotlin 공식 사이트에서는](https://kotlinlang.org/) 간결함, 안정성, 그리고 JVM, Android 와 같은 기존의 존재하는 플랫폼 위에서 호환성을 이야기한다.
그럼 하나씩 짚어보자.

## 간결함(Concise)

코틀린은 [boilerplate]([https://ko.wikipedia.org/wiki/%EC%83%81%EC%9A%A9%EA%B5%AC_%EC%BD%94%EB%93%9C](https://ko.wikipedia.org/wiki/상용구_코드)) 코드를 상당히 줄여준다. 코드를 작성하다 보면 보일러플레이트 코드가 생기기 마련이다. 이것이 나쁘다는 것이 아니라 변하지 않는 항상 같은 패턴의 코드는 직접 작성을 하지 않고 도구의 도움을 받자는 것이다.
예를 살펴보자.

### data class

- Pure java

```java
  public class DataExample {
    private final String name;
    private int age;
    private double score;
    private String[] tags;

    public DataExample(String name) {
      this.name = name;
    }

    public String getName() {
      return this.name;
    }

    void setAge(int age) {
      this.age = age;
    }

    //...
    public String[] getTags() {
      return this.tags;
    }

    public void setTags(String[] tags) {
      this.tags = tags;
    }

    @Override public String toString() {
      return "DataExample(" + this.getName() + ", "
        + this.getAge() + ", " + this.getScore() + ", "
        + Arrays.deepToString(this.getTags()) + ")";
    }

    protected boolean canEqual(Object other) {
      return other instanceof DataExample;
    }

    @Override public boolean equals(Object o) {
      //...
      return true;
    }

    @Override public int hashCode() {
     //...
    }
```

  위와 같이 java 에서 data 를 표현하는 클래스는 보일러 플레이트 코드가 상당부분 존재한다.
  필요한 부분이지만 귀찮은 건 사실이다. 이런 귀찮음을 느낀 훌륭한 개발자들이 좋은 도구를 개발했다.

- With [lombok](https://objectcomputing.com/resources/publications/sett/january-2010-reducing-boilerplate-code-with-project-lombok)

```java
  @Data public class DataExample {
    private final String name;
    @Setter(AccessLevel.PACKAGE) private int age;
    private double score;
    private String[] tags;
```

  바로 lombok 이다. 위 코드는 pure java 코드와 동일하다.
  이제 코틀린을 볼 차례이다.

- kotlin

```kotlin
  data class DataExample(
    val name: String, var score: String?,
    var tags: Array<String>?
)
```

위에서 보았던 java 코드와 동일한 효과를 낸다.
  정말 간결한지 않은가?

  data class 는 DTO, VO 등의 패턴을 쉽게 사용가능하도록 제공해주는 언어적 요소이다.
    equals / hashCode, toString 등을 자동으로 생성해준다.

  > 잘가 lombok~

### singleton

- java

```java
  public class Something {
      private Something() {}

      private static class LazyHolder {
          static final Something INSTANCE = new Something();
      }

      public static Something getInstance() {
          return LazyHolder.INSTANCE;
      }
  }
```

  Java 에서 Singleton Pattern 을 검색해보라. 얼마나 많은 지식과 많은 구현체를 볼 수 있는가?

  > [자바 싱글턴 패턴](https://www.journaldev.com/1377/java-singleton-design-pattern-best-practices-examples)

- kotlin

```kotlin
  object DataProviderManager {
      fun registerDataProvider(provider: DataProvider) {
          // ...
      }

      val allDataProviders: Collection<DataProvider>
          get() = // ...
  }
```

  object 키워드 하나면 끝이다.

  간결해 보이는가? 위에서 말한 것 이외에도 람다의 간결한 표현, 늦은 초기화 등 충분히 간결한 문법이 많이 존재한다.

## 안전성(Safe)

비교적 최근에 탄생한 언어 답게 안전성에 대한 철학이 좋다.
Null safety, 스마트 캐스트, 불변 등 편리한 기능을 문법적으로 제공해준다.
하나씩 살펴보자.

### Null-safety
```kt
var output: String
output = null   // Compilation error
```

```kt
val name: String? = null    // Nullable type
println(name.length())      // Compilation error
```

kotlin 은 compile 시점에 NPE 을 검증하고 방어한다.

> Tony Hoare 는 Null 참조를 만든 것을 'billion-dollar mistake.' 라 부른다.[wikipedia](https://en.wikipedia.org/wiki/Tony_Hoare)

### 스마트 캐스트(Smart cast)

- java

```java
Object obj = "The quick brown fox jumped over a lazy dog";
if(obj instanceof String) {
    String str = (String) obj;
    System.out.println("Found a String of length " + str.length());
}
```
  위 코드는 자바에서 instanceof 연산자를 통해 obj 가 String 인지 구문을 체크하고 맞다면 형변환을 해서 obj 를 사용하는 코드다.
  obj 가 String 의 객체인 것이 확인 되었는데 또 형변환을 해야 할까?
  kotlin 코드를 살펴보자

- kotlin
```kotlin
val obj: Any = "The quick brown fox jumped over a lazy dog"
if(obj is String) {
    println("Found a String of length ${obj.length}")
}
```
  그렇다. 의미 없는 캐스팅은 필요 없다. 이제 왜 스마트 캐스트라 부르는지 감이 오는가?

## 상호운용성(Interoperable)

새로운 언어의 성공여부는 해당 언어로 프로그램을 작성함에 있어 풍부한 라이브러리와 견고한 프레임워크가 아닐까 하는 생각이든다.

### JVM

긴 세월동안 OOP 의 부흥과 같이 해온 JAVA 가 만들어 놓은 생태계는 가히 어마어마 하다고 말할 수 있다. 그런 JVM 과 호환은 kotlin 의 큰 장저이 아닐까 한다.

    .kt -> Kt compiler -> .class (Java byte)
    -> JVM (with kt runtime library)

Kotlin compiler 는 kt 를 java byte 코드로 compile 하고 java 6+ 를 지원한다.

> Spring, Vert.x 등 공식적으로 kotlin 을 지원하는 프로젝트들이 늘어나고 있다.


### Android

안드로이드 역시 kotlin 을 공식언어로 지정한바 있다.

    .kt -> Kt compiler -> .class -> .dex
    -> dex23oat -> art (from android 5.0)

자바와 마찬가지로 kt 를 java byte 코드로 compile 하기 때문에 안드로이드에서도 사용할 수 있다.

![java8 호환표](https://raw.githubusercontent.com/gmunch/gmunch.github.com/master/assets/images/android_java8.png)

> Android 에서는 minSdk 버전을 올리지 못하는 프로젝트에서는 kotlin 을 사용하여 Java 8 의 Stream 처리와 같은 연산을 쉽게 할 수 있다.  

### Other
