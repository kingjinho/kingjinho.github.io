---
layout: default
title: navigation backstack & popup
parent: Compose
nav_order: 5
grand_parent: Android
---

# popup & navigation backstack

<video src="./img/error.mp4" controls="controls" style="max-width: 300px;"></video>

---

## 인트로

영상처럼 compose 공부를 재미나게 하기 위해 여러가지 게임을 만들어보고있다.
그러던 중 땅따먹기 게임이 도전해볼만해서 만들어봤다.

만들면서 마주쳤던 다른 이슈는 [fillmaxXX: 왜 꽉 안채우지 너?](../4.fillmax/index.html)에 적어놨다.

이번에 하면서 마주쳤던 이슈는 Popup composable이다.

## 문제

1. 게임이 끝나면 파란색이 이겼는지, 빨간색이 이겼는지를 popup으로 보여준다
2. Popup composable이 나온뒤에 뒤로가기를 누르면 이전 화면이 나오지만 나중에 사라진다.

## 문제 코드

```kotlin
@Composable
fun SomeScreen() {

    if (if_condition_is_met) {

        Popup(
            alignment = Alignment.Center,
            properties = PopupProperties(
                focusable = false,
            )
        ) {
            Column(
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Text(text = "Game Over!!!!")
                Text(text = if (playerRedHeight == MAXIMUM_HEIGHT_WEIGHT) "Red Win!!!" else "Blue Win!!!")
                Spacer(modifier = Modifier.height(16.dp))
                Button(onClick = {
                    playerRedHeight = 0.5f
                }) {
                    Text(text = "Restart")
                }
            }
        }
    }

}

```

## 과거의 나에게 묻는 질문

- PopupProperties(focusable = false)로 했는데 이렇게하면 밖에 영역이 터치되어진다. 심지어 default 값도 false다. 왜 그랬니?

## 앱 전체 구조

- Activity
    - Theme
        - NavHost
            - Screen1
            - Screen2
            - ...

# 뒤로가기는 왜 동작했을까?

PopupProperties의 focusable값을 false로 줘서 뒤로가기 소프트 버튼 클릭이나 제스쳐가 Screen 레벨에서 잡혔다.

즉 NavHost의 BackHandler Composable로 정의된 navController.popBackStack()이 호출되었다.

## 조금은 더 디테일한 이유

```kotlin
@Immutable
class PopupProperties @ExperimentalComposeUiApi constructor(
    val focusable: Boolean = false,
    val dismissOnBackPress: Boolean = true,
    val dismissOnClickOutside: Boolean = true,
    val securePolicy: SecureFlagPolicy = SecureFlagPolicy.Inherit,
    val excludeFromSystemGesture: Boolean = true,
    val clippingEnabled: Boolean = true,
    val usePlatformDefaultWidth: Boolean = false
) {
    ...
}
```
focusable = true라는 가정하에
1. `dismissOnBackPress = true`
    - dispatchKeyEvent() 호출 및 내부에서 Popup composable 생성시 넘겨준 nullable onDismissRequest 람다 호출
2. `dismissOnClickOutside = true`
    - onTouchEvent() 호출 및 내부에서 Popup composable 생성시 넘겨준 nullable onDismissRequest 람다 호출
3. `하지만 둘 중 하나가 false`면
    - 다 건너뛰고 각 함수(dispatch.., touch..)의 super.XX() 호출
4. `focusable이 false` 면
    - 두 함수 어느것에도 반응하지 않는다.
    - 왜?
        - Popup composable 내부는 PopupLayout으로 이루어져있는데 팝업을 생성하는 과정에서 `setIsFocusable(Boolean)`함수 호출
        - 여기서 window의 flag 값을 업데이트 and 연산자로 업데이트

[터치 이벤트 동작 정리](../6.touch-and-keyevent/index.html)


## 솔루션

<b>Reminder</b>: 더 나은 방법이 존재할거같은데 아직 Compose에 익숙하지 않아서 좋은 방법인지는 모르겠다.
나중에 여러 개념들을 확실하게 알고 좀 더 적응이 된다면 다시 고민해봐야할 포인트로 남겨 둬야겠다.

### 결국 핵심은 Popup을 먼저 닫는 것

맨 위영상에서도 볼 수 있듯이 Popup 밑에 깔려있는 게임 화면이 사라지고 난 뒤에 사라진다.

`왜?`

그 이유는 Popup Composable의 구현에 있는데, `팝업이 누구에 의해서 닫히는 지를 보면 DisposableEffect에 의해 닫히게 된다.`

Side-effects에 관한 설명은 지금 내 지식선에서 설명하기는 확실히 아니기에 아래 링크를 통해 이해하는것이 조금 더 빠를것같다.

간략하게 설명하면 Side-effect는 Composable 범위 밖에서 일어나는 State의 변화를 Side-effect, 또는 부수효과라고 부르는데
기본적으로 Composable은 Side-effect의 발생을 지양해야하지만, 경우에 따라 이 Side-effect가 필요한데 그때 Side-Effect
API를 이용하여 작업을 진행한다.

`DisposableEffect`는 side effects composable이 Composition을 떠나거나 key가 바뀐 후 회수 되어야 할 경우에 사용한다.
지금 내 케이스는 Composition을 벗어나는 경우이다.(onForgotten())

Screen에서 나올 때 Composition을 벗어나므로, 벗어나고 난 후에 아래 코드 스니펫의 onDispose { }의 dismiss()가 호출된다.

[Side Effects 설명](https://tourspace.tistory.com/412)

```kotlin
@Composable
fun Popup(...) {
    ...
    DisposableEffect(popupLayout) {
        popupLayout.show()
        popupLayout.updateParameters(
            onDismissRequest = onDismissRequest,
            properties = properties,
            testTag = testTag,
            layoutDirection = layoutDirection
        )
        onDispose {
            popupLayout.disposeComposition()
            // Remove the window
            popupLayout.dismiss()
        }
    }
    ...
}
```

자 그럼 다 나왔다. 왜 팝업이 늦게 사라지는 지 알았으니, 이제 Screen이 먼저 사라지기 전에 Popup을 없애면 된다.
```kotlin
@Composable
fun HopScotchGameScreen(
    ...
    onBackPressedWhenPopupIsOpen: () -> Unit = {}
) {
    var playerRedHeight by remember {
        mutableFloatStateOf(0.5f)
    }

    var isBackButtonPressed by remember {
        mutableStateOf(false)
    }

    Column {

        //...게임 화면...
        
        if (!isBackButtonPressed && isGameOver(playerRedHeight)) {
            Popup(
                alignment = Alignment.Center,
                properties = PopupProperties(focusable = true),
                onDismissRequest = {
                    isBackButtonPressed = true
                    onBackPressedWhenPopupIsOpen()
                }
            ) {
                게임 끝났을시 관련 Composable
            }
        }
    }
}
```

- focusable을 true로 하여 onDismissRequest가 호출 되게한다.
- 하나의 Boolean State 변수를 더 만들어 backButton이 클릭이 되었는지 업데이트한다.
- PopupProperties의 focusable, dismissOnBackPressed 가 true면 뒤로가기 버튼 클릭 keyevent를 캐치하도록 Popup 내부 구현이
되어있으니, Screen레벨에서 lambda로 navController.popBackStack()을 넘긴다.

```kotlin
@Composable
fun PortfolioApp() {
    val navController = rememberNavController()
    NavHost(navController = navController, startDestination = Screen.Home.route) {
        
        ...

        composable(Screen.HopScotchGame.route) {
            HopScotchGameScreen {
                navController.popBackStack()
            }
        }

            ...

    }
}
```

간단한 이슈인줄알고 써봐야했다가 식겁했다

