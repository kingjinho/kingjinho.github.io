---
layout: default
title: Primitives (WIP)
parent: Kotlin
nav_order: 2
---

# 기본 타입

---

- Java에서는 원시 타입, 그리고 참조 타입에 대한 명확한 구분이 있다.
    - 스택에 저장되는 원시 타입, 힙 영역에 할당된 메모리를 가르키는 참조 타입
- `하지만 Kotlin에서는 똑같은 타입이라도 때에 따라 원시 타입이 될 수도, 참조 타입을 수도 있다.`
    - Java: int, Integer
    - Kotlin: Int이지만 필요할 때 박싱 수행
- 모든 Kotlin 타입은 `Any` 라는 내장 타입의 직간접적인 하위 타입

### 부동 소수점

- Java의 float, double
- 디폴트 타입은 Double
- Float은 자동으로 Double로 변환되지않는다.

### 연산

- 단한 +/- 결과는 기본적으로 인자들 타입과 같지만, Byte, Short의 경우만 `Int`를 내놓는다.
```kotlin

fun main() {
    val a: Byte = -1
    val b = -a // b는 Int
}

```

### 비트 연산

[비트연산의 활용](https://shoark7.github.io/programming/knowledge/some-useful-bit-tricks-and-usages)