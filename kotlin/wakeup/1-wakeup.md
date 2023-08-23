---
layout: default
title: 시리즈1 - 배열, 리스트 순회
parent: Wake Up
grand_parent: Kotlin
nav_order: 1
---

# 배열 리스트 순회시 내가 저지른 멍청한 실수

---

코딩문제를 한번 풀어보다가 어? 로직이 맞는데 답이 이상한데 라고 찾아보니

배열, 리스트의 index를 끝에서부터 아래로 내려 갈 때 이상하게 쓰고 있었다.

```kotlin
fun 코딩문제() {
    ...
    for(i in nums.lastIndex..0) { //문제의 부분. 쏘 스뜌핃!
        right = if(i == nums.lastIndex) {
            right
        } else {
            right * nums[i+1]
        }
        result[i] *= right
    }
        ...   
}

```

downTo로 해서 썼었어야했는데..

Android Studio나 IntelliJ에서 자동완성 기능이 있고, 코파일럿까지 같이 쓰다보니 기본적인 문법도 멍청하게 까먹는 경우가 종종있다.

사실은 핑계고 애초에 문법을 제대로 숙지하지 못하고 있는 내 문제다.

다시는 이런 실수 하지 말길 희망하는 차원에서 적어본다..!

[코딩 문제](https://leetcode.com/problems/product-of-array-except-self/solutions/65622/simple-java-solution-in-o-n-without-extra-space/)