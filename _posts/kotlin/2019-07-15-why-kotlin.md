---

layout: default
title: 왜 코틀린을 써야 할까?
date: 2019-07-15 21:05:00
lastmod : 2019-07-16 23:00:00
sitemap :
  changefreq : daily
  priority : 1.0
  
---

# 왜 코틀린을 써야 할까?

먼저, 코틀린을 왜 써야하는지 개인적인 생각을 적기 이전에 코틀린 공식 사이트에서 이야기 하는 코틀린을 왜 써야 하는가?에 대해서 알아보겠습니다.

![](https://raw.githubusercontent.com/gmunch/gmunch.github.com/master/assets/images/kotlinadvantage.png)

[kotlin 공식 사이트에서는](https://kotlinlang.org/) 간결함, 안정성, 그리고 JVM, Android 와 같은 기존의 존재하는 플랫폼 위에서 호환성을 이야기합니다.
그럼 하나씩 짚어보겠습니다.

## 간결함(Concise)

코틀린은 [boilerplate]([https://ko.wikipedia.org/wiki/%EC%83%81%EC%9A%A9%EA%B5%AC_%EC%BD%94%EB%93%9C](https://ko.wikipedia.org/wiki/상용구_코드)) 코드를 상당히 줄여줍니다. 코드를 작성하다 보면 보일러플레이트 코드가 생기기 마련입니다. 이것이 나쁘다는 것이 아니라 변하지 않는 항상 같은 패턴의 코드는 직접 작성을 하지 않고 도구의 도움을 받자는 것입니다.
예를 살펴보겠습니다.

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

  위와 같이 java 에서 data 를 표현하는 클래스는 보일러 플레이트 코드가 상당부분 존재합니다.
  필요한 부분이지만 귀찮은 건 사실입니다. 이런 귀찮음을 느낀 훌륭한 개발자들이 좋은 도구를 개발했습니다.

- With [lombok](https://objectcomputing.com/resources/publications/sett/january-2010-reducing-boilerplate-code-with-project-lombok)

```java
  @Data public class DataExample {
    private final String name;
    @Setter(AccessLevel.PACKAGE) private int age;
    private double score;
    private String[] tags;
```

  바로 lombok 입니다. 위 코드는 pure java 코드와 동일합니다.
  이제 코틀린으로 보겠습니다.

- kotlin

```kotlin
  data class DataExample(
    val name: String, var score: String?,
    var tags: Array<String>?
)
```

위에서 보았던 java 코드와 동일한 효과를 냅니다.
  정말 간결한지 않습니까?

  data class 는 DTO, VO 등의 패턴을 쉽게 사용가능하도록 제공해주는 언어적 요소입니다.
    equals / hashCode, toString 등을 자동으로 생성해줍니다.

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

  Java 에서 Singleton Pattern 을 검색해보십시요. 얼마나 많은 지식과 많은 구현체가 있는지 알게됩니다.

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

  object 키워드 하나면 끝입니다.

  간결해 보이나요? 위에서 말한 것 이외에도 람다의 간결한 표현, 늦은 초기화 등 충분히 간결한 문법이 많이 존재합니다.

## 안전성(Safe)

비교적 최근에 탄생한 언어 답게 안전성에 대한 철학이 훌륭합니다.
Null safety, 스마트 캐스트, 불변 등 편리한 기능을 문법적으로 제공해주고있습니다.
하나씩 살펴보겠습니다.

### Null-safety
```kt
var output: String
output = null   // Compilation error
```

```kt
val name: String? = null    // Nullable type
println(name.length())      // Compilation error
```

kotlin 은 compile 시점에 NPE 을 검증하고 방어합니다.

> Tony Hoare 는 Null 참조를 만든 것을 'billion-dollar mistake.' 라 부릅니다.[wikipedia](https://en.wikipedia.org/wiki/Tony_Hoare)

### 스마트 캐스트(Smart cast)

- java

```java
Object obj = "The quick brown fox jumped over a lazy dog";
if(obj instanceof String) {
    String str = (String) obj;
    System.out.println("Found a String of length " + str.length());
}
```
  위 코드는 자바에서 instanceof 연산자를 통해 obj 가 String 인지 구문을 체크하고 맞다면 형변환을 해서 obj 를 사용하는 코드입니다.
  obj 가 String 의 객체인 것이 확인 되었는데 또 형변환을 해야 할까요?
  kotlin 코드를 살펴보겠습니다.

- kotlin
```kotlin
val obj: Any = "The quick brown fox jumped over a lazy dog"
if(obj is String) {
    println("Found a String of length ${obj.length}")
}
```
  그렇습니다. 의미 없는 캐스팅은 필요 없습니다. 이제 왜 스마트 캐스트라 부르는지 감이 오시나요?

## 상호운용성(Interoperable)

새로운 언어의 성공여부는 해당 언어로 프로그램을 작성함에이써 풍부한 라이브러리와 견고한 프레임워크가 아닐까 하는 생각이듭니다.

### JVM

긴 세월동안 OOP 의 부흥과 같이 해온 JAVA 가 만들어 놓은 생태계는 가히 어마어마 하다고 말할 수 있습니다. 그런 JVM 과 호환은 kotlin 의 큰 장점이 아닐까 합니다.

    .kt -> Kt compiler -> .class (Java byte)
    -> JVM (with kt runtime library)

Kotlin compiler 는 kt 를 java byte 코드로 compile 하고 java 6+ 를 지원합니다.

> Spring, Vert.x 등 공식적으로 kotlin 을 지원하는 프로젝트들이 늘어나고 있습니다.


### Android

안드로이드 역시 kotlin 을 공식언어로 지정한 바 있습니다.

    .kt -> Kt compiler -> .class -> .dex
    -> dex23oat -> art (from android 5.0)

자바와 마찬가지로 kt 를 java byte 코드로 compile 하기 때문에 안드로이드에서도 사용할 수 있습니다.

![java8 호환표](https://raw.githubusercontent.com/gmunch/gmunch.github.com/master/assets/images/android_java8.png)

> Android 에서는 minSdk 버전을 올리지 못하는 프로젝트에서는 kotlin 을 사용하여 Java 8 의 Stream 처리와 같은 연산을 쉽게 할 수 있습니다.  

### Other(Browser / Native)

아직 갈길이 멀었지만 ECMAScript 2015 를 목표로 Browser 플랫폼을 지원하려고 노력중이며, IOS, MacOS, Android, Linux, WebAssembly 와 같은 다양한 플랫폼을 지원하려 하고 있습니다.

# 마무리

자바를 완벽하게(kotlin 주장) 호환하면서 깔끔한 문법과 안전성 그리고 강력한 IDE(IntelliJ) 지원 등을 생각해보면 사용하지 않을 이유를 찾는게 더 어렵지 않을까요?

IntelliJ 에 있는 대부분의 기능은 Eclipse 에도 있는데 IntelliJ 를 써보면 Eclipse 로 돌아갈 순 없듯이, Kotlin 을 써보면 Java 로 돌아가기는 어려울 것입니다.

*일단 써보면 자바의 어두운 미래가 보일 것입니다.*
