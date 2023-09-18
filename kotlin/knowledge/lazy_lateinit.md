---
layout: default
title: lateinit vs lazy
nav_order: 3
has_children: false
parent: Knowledge
grand_parent: Kotlin
---

# lateinit vs lazy

---

## 원라이너

- 둘 다 변수의 생성과 동시에 각각의 값을 초기화 하는게 아닌 필요한 시점에 값을 설정하는 개념은 같지만,
- lazy는 동기화를 지원하고 lateinit은 그렇지 않으며.
- 그리고 lateinit은 가변성이고 lazy는 반대로 불변이다.

- lateinit은 특히 값이 설정되지 않은채 사용하게 될 시 exception을 발생시킨다.

## 늦은 초기화는 언제 사용?

소프트웨어의 개발상 굳이 변수를 선언하는 시점에 초기화를 해야 할 필요는 없고 경우데 따라서는 사용 직전에 초기화를 할 수도 있다.

또는 DI를 통해 변수가 객체를 set 할 경우도 있다.

즉, 객체나 변수의 생성, 선언 시점에는 초기화 하기가 애매하거나 까다롭지만, 그렇다고해서 해당 객체나 변수의 사용 시점에는 무조건 non-null일 경우에
사용하게 된다.

그래서 코틀린의 경우 2가지 옵션을 제공한다: lateinit, lazy

## lateinit

- 사용전에는 초기화가 될꺼다.
- 그리고 그 값은 가변. 초기화 되고 난 다음에도 언제든지 변경이 가능하다.
- 초기화가 되기 전까지 값은 null로 세팅되어있다 (kotlin decompile)
- [원시타입은 불가능하다.](#lateinit에-원시타입-사용-불가능-왜때문)

## lazy

- 사용전에는 초기화가 될꺼다.
- 하지만 초기화가 된 값은 불변. 최초 한번만 초기화가 가능하고 끝나면 되고나면 값을 
    - 참조형의 경우 메모리 참조값을 바꿀 수 없다.

```kotlin
lateinit var number: String

val textLength by lazy {
    number.length
}

number = "asdf"

println(textLength) // 4

number = "asdff"

println(textLength) //4. 한번 초기화 되고 난다음에는 바뀌지 않는다.

```

## 자료형 뒤에 ?붙여서 표현하기
```kotlin
val x: String? = null
```
사실 이것도 하나의 방법이긴하다. 근데 이 방식으로 개발 했을때 경험상 썩 좋지 않았다고 생각했던 부분은
1. 계속 null 체크를 해줘야하며.
2. 그리고 만약에 이 변수 또는 객체가 사용 될때는 `무조건 non-null이라면` 데이터 타입에 ?를 붙이는것도 이상하고 사용할때마다 !!(non-null assertion)
을 계속 써줘야하는 것도 여간 불편한게 아니다. 

`즉 애초에 나중에 초기화가 될걸 알고있는데 왜 데이터 형을 nullable로 하는것에 대한 문제이다.`

## lateinit에 원시타입 사용 불가능 왜때문?

- lateinit을 자바코드로 변환해보면 null체크를 하는데, 자바에서 원시타입은 디폴트 값을 가지고 있고 null값을 허용하지 않는다.
- 즉 lateinit에서는 null을 이용하여 초기화 여부를 체크하는데 사용하는데 원시타입으로는 초기화 여부를 체크할수없으니깐


## 동기화 여부

- lazy에서만 동기화가 가능
- 물론 lazy에 옵션이 있긴하지만 디폴트로 설정할시 동기화 지원


## 링크

- [[Kotlin] 🤚🏻 lateinit vs lazy, 정확히 아세요?](https://velog.io/@haero_kim/Kotlin-lateinit-vs-lazy-%EC%A0%95%ED%99%95%ED%9E%88-%EC%95%84%EC%84%B8%EC%9A%94)
- [[내 맘대로 정리한 Kotlin] lateinit과 by lazy의 차이점](https://holika.tistory.com/entry/%EB%82%B4-%EB%A7%98%EB%8C%80%EB%A1%9C-%EC%A0%95%EB%A6%AC%ED%95%9C-Kotlin-lateinit%EA%B3%BC-by-lazy%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [lateinit과 by lazy의 차이가 무엇인가요?](https://todaycode.tistory.com/171)
- [Lazy vs Eager Initialization](https://dev.to/albertbennett/lazy-vs-eager-initialization-abn)
- [late-initialized properties and variables](https://kotlinlang.org/docs/properties.html#late-initialized-properties-and-variables)