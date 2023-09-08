---
layout: default
title: kotlin property
nav_order: 2
has_children: false
parent: Knowledge
grand_parent: Kotlin

---

# Kotlin Property(feat. java)

---

## 원라이너

- 데이터를 저장하는 필드와 접근자(getter, setter)를 모두 포함하는 변수로 mutable, immutable로 선언이 가능하며 명시적으로 접근자를 선언 할 수 있다.
- 초기값이 필요하지만 lateinit이 붙으면 안해도 된다.

## 무엇

변수와 비슷한 놈. 근데 변수는 아니고, 요상하게 기분 나쁜녀석이다.

- 필드와 접근자를 통칭하는 녀석
- 자바에서는 데이터를 필드에 저장
    - 그리고 getter, setter가 있다면 이를 접근자라고 부른다.
        - private 데이터들은 getter, setter를 통해 접근
- 생성자에 val, var가 없는 매개변수가 있다면?
    - 프로퍼티가 아니기에 init 블록이나, 프로퍼티 초기화에서 사용하는거 말고는 사용하지 못한다.
-

## 어떻게 변환되는가

사실 kotlin으로 생성한 프로퍼티에는 다양한 케이스가 존재한다.

생성자에서 가변,불변,

## backing field

- 프로퍼티에 값을 저장하는 변수를 칭하는 키워드
- field라는 단어로 주로 사용되며, get, set 에서만 사용 가능
- 항상 생성되는것은 아니고 getter, setter 중 하나를 사용하거나, 커스텀 접근자가 field 키워드를 통해 접근 할 때 생성

## backing property

- 기본 접근자, 또는 커스텀 접근자를 통해 프로퍼티에 접근할때의 제약은 리턴 타입이 같아야 한다는 단점이있다.
- 만약 클래스 내부에 `var`로 선언되어있는 프로퍼티가 내부에서만 수정이 가능하고 외부에는 공개만(수정X) 되어야 한다면, 이때
  backing property 를 사용한다.

```kotlin
private var _results: MutableList<List<Tile>> = mutableListOf()

//이렇게 되면 results는 자바 코드로 변환시 필드가 생성되지않고 getResults()로 만들어진다. 
val results: List<List<Tile>>
    get() = _results
```

### 링크

- [코틀린 프로퍼티(kotlin property)](https://wooooooak.github.io/kotlin/2019/05/24/property/)
- [[Kotlin] Backing Field와 Backing Properties (wordle 미션)](https://colour-my-memories-blue.tistory.com/6)
- [Kotlin 기초강의#5 :: 프로퍼티(Property)와 뒷받침하는 필드(Backing Field)](https://manorgass.tistory.com/80)
- [Why compile time constants limited to only primitives and Strings?](https://stackoverflow.com/questions/34429097/why-compile-time-constants-limited-to-only-primitives-and-strings)