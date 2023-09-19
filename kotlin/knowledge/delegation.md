---
layout: default
title: 위임 - Delegation
nav_order: 4
has_children: false
parent: Knowledge
grand_parent: Kotlin
---

# 위임 - Delegation

---

## 원라이너
`객체가 해야할 일을 다른 객체에게 위임하여 실행하게 하는 디자인 패턴` 

## 무엇

- 디자인 패턴중에 하나로 객체 합성을 상속에 장점중 하나인 코드 재활용성을 높이기 위해 고안된 패턴 중 하나

## 언제

- by 키워드를 사용한 프로퍼티에서 활용
- 인터페이스를 클래스 위임에 사용하는 경우

## 어떻게
1. 프로퍼티에서의 활용
   - by 키워드를 사용하여 프로퍼티의 getter(val), setter(var)를 위임한다.
     - 예시) lazy
   
2. 인터페이스를 클래스 위임에 사용하는 경우
   - 상속을 대체(대안)하기 위해 사용
   - 기본적으로 kotlin 클래스는 final

## 합성(composition)과 위임(delegation)의 차이

- Delegation = Composition + forwarding
  - Forwarding =  다른 클래스의 메서드를 호출하여 결과 반환

## 유의점

```kotlin
interface Base {
    val message: String
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override val message = "BaseImpl: x = $x"
    override fun print() { println(message) }
}

class Derived(b: Base) : Base by b {
    override val message = "Message of Derived"
}

fun main() {
    val b = BaseImpl(10)
    val derived = Derived(b)
    //b는 BaseImpl을 구현체로 사용하기에 Dereived에서 message 내용을
    //바꾸던 말던  알수있는 방법이 없다. 
    //그래서 "BaseImpl: x = $x"이 출력된다.
    derived.print()
    println(derived.message)
}
```


### 참조
[Delegation이 무엇인지 아시나요? (상속과 구성까지 알아보자)](https://todaycode.tistory.com/175)


