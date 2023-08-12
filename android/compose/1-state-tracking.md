---
layout: default
title: State (WIP)
parent: Compose
nav_order: 1
grand_parent: Android
---

# State tracking

코드랩 기반

---

- Composition: Composable을 실행할때 jetpack compose가 만드는 일종의 UI 설명서
    - 어떤게 필요하고, 무엇을 화면에 나타내어야 하는지에 대한 청사진
    - Composables를 분석을 통해 tree-structure ui 계층도를 생성
- Initial Composition: 최초 Composition
- Recomposition: 상태(state, parameters) 변화에 따른 Composition의 update
    - 필요하다면, 다시 UI를 그려야 한다.

## State

- 상태라고도 불리는데
- 시간이 지남에 따라 바뀔수있는 값이라고 생각해도 상관없고
- 데이터라고 해도 이해하는데 큰 무리는 없다.
- 컴퓨터 내에 저장된 파일을 수정 하면 파일이 상태가 될 수 있고, 유저의 액션에 따라 값이 바뀌는 변수도 상태의 예시가 될 수 있다

## 기존의 우리가 생각하던 mutable 변수(상태)의 변경

```kotlin
@Composable
fun updateNumber() {
    var num = 0

    Button(onClick = { num++ }) {
        Text("Increment")
    }
}

```

- 동작하지 않는다.

## Compose 방식

- 이렇게 하면 뭔수로 Compose가 `num` 이 바뀌었는지 알 수 없다.
- Compose는 state(또는 data)의 변경을 체크하고 그에 따른 recomposition을 위해 `State`, `MutableState`라는 걸 사용한다.

```kotlin
@Composable
fun updateNumber() {
    var num: MutableState<Int> = mutableStateOf(0)

    Button(onClick = { num.value++ }) {
        Text("Increment")
    }
}

```

- 이렇게 해도 recomposition은 진행되지만, num의 숫자는 바뀌지않는다. 이유는 Composable이 recomposition 될때마다 num을 초기화
  하기 때문이다.
    - 즉, 단순히 `State<T>`만 구현한 변수는 Composition에서 값을 저장하지만 recomposition에서는 값을 잊어버린다(forget).
- 따라서 recomposition에서도 num의 값을 유지하기 위해 `remember` 키워드를 사용하여 다음과 같이 바꾸면, 우리가 예상하는대로 동작한다.

```kotlin
@Composable
fun updateNumber() {
    var num by remember { mutableStateOf(0) }

    Button(onClick = { num++ }) {
        Text("Increment")
    }
}

```

## remember 키워드?

- 위 예시에서 보았듯이 recomposition에서도 상태(data) 값을 유지하기 위해 `remember` 키워드를 사용하였다.
- `remember` 키워드를 상태(data)는 initial composition에서 값을 저장하고 recomposition 상태에서 저장된 값을 리턴한다.
- 그리고 또 한가지 `remeber` 키워드를 사용한 상태에서 알아야 할 점은
    - 호출한 composable이 composition 스텝에서 사라지면 자동으로 사라진다
- 예시)

```kotlin

@Composable
fun WaterCounter(modifier: Modifier = Modifier) {

    Column(modifier = modifier.padding(16.dp)) {
        var count by remember { mutableStateOf(0) }

        // showTask 위치 주목:
        var showTask by remember { mutableStateOf(0) }

        if (count > 0) {
            if (showTask) {
                WellnessTask(onClick = { showTask = false }, taskName = "Drink water")
            }
            Text("You've had $count glasses.")
        }

        Button(onClick = { count++ }, Modifier.padding(top = 8.dp), enabled = count < 10) {
            Text("Add one")
        }

        Button(onClick = { count = 0 }, Modifier.padding(top = 8.dp)) {
            Text("Clear")
        }
    }
}

@Composable
fun WaterCounter(modifier: Modifier = Modifier) {

    Column(modifier = modifier.padding(16.dp)) {
        var count by remember { mutableStateOf(0) }

        if (count > 0) {
            // showTask 위치 주목:
            var showTask by remember { mutableStateOf(0) }
            if (showTask) {
                WellnessTask(onClick = { showTask = false }, taskName = "Drink water")
            }
            Text("You've had $count glasses.")
        }

        Button(onClick = { count++ }, Modifier.padding(top = 8.dp), enabled = count < 10) {
            Text("Add one")
        }

        Button(onClick = { count = 0 }, Modifier.padding(top = 8.dp)) {
            Text("clear")
        }
    }
}

```

- `showTask`선언 위치를 잘보면 하나는 column, 하나는 if 절에서 선언하였다.


- 첫번째 예시에서 `WellnessTask` Composable은 **onClick이 호출되고, clear 버튼을 누르고, 다시 add one 버튼을 눌러도 보여지지 않는다**.
    - 이유는 count > 0 if문이 composition 단계에서 지워졌기 때문이다.
    - 즉 `Column`Composable이 composition단계에서 지워지지 않았기 때문이다.


- 두번째는 `WellnessTask` Composable은 위에서 설명한 step을 해도 다시 보여진다.
    - 이유는 count > 0 if문이 composition 단계에서 지워졌기 때문에, count의 숫자가 다시 높아질때마다 composition단계로 들어오면서 새로 생성이 되기 때문이다.

- `LazyColumn` Composable에서도 마찬가지로 화면에 보이지 않으면 Composition에서 벗어나기에 remember로 설정한 값이 있으면
  해당 item의 업데이트 된 상태값은 초기화가 되어버린다.

## rememberSaveable

- remember는 recomposition에서도 상태 값을 유지하기 위해 사용
    - 하지만 화면 회전을 포함한 `activity recreation`의 경우에는 모든 상태(data)가 초기화 되어버림
- `activity recreation`에서도 상태(data)를 유지 시키기 위해 rememberSaveable을 사용
    - `Bundle`을 사용하여 상태(data) 저장
- recomposition에서도 상태(data)는 보존되고, `activity, process recreation`의 경우에도 상태(데이터) 보존

## State hoisting

- Stateful, Stateless composable
    - <span style="color:#A084E8">Stateless</span>: 상태(data)를 가지고 있지 않으며, 상태에 대한 행동이 없음
        - 상태(data)를 가지고 있지않다?
            - composable 함수 내부에서 선언하는 상태가 없다는 말.
            - 즉, **parameters로 상태(data)와 상태 값 변경을 담당하는 action을 전달만 받아 그대로 UI로 보여주기만 한다는 말**
    - <span style="color:#A084E8">Stateful</span>: 상태(data)를 가지고 있으며, 상태를 변경하는 행동도 가지고 있음
- State hoisting은 UI, Business 로직에 의존
- 기본적으로는 recomposition을 최소화 하기 위해 공통되는 최소 계층에 ui state를 둔다.
    - 이때 계층은 ui logic에만 의존하면 composable일 수도있고, business logic도 연관이 있다면 viewmodel이 될 수도 있다.
- 그리고 때에 따라 state가 여러 곳에서 쓰이지않다면, hoist를 안해도 상관없다.
