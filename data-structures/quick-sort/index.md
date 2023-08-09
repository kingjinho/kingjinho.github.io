---
layout: default
title: Quick Sort (WIP)
nav_order: 1
has_children: true
parent: Data Structure
---

# Quick sort

---

- 버블 정렬, 선택 정렬를 포함한 정렬 알고리즘이 있지만, 실제로 정렬할때는 쓰지 않는다.
- 언어마다 내장된 정렬 함수가 있으니깐.
- 언어의 대부분은 퀵 정렬을 내부 정렬 함수 알고리즘에 사용한다.

## 굳이 구현할 필요가 없는데 알아보는 이유?

- 동작 방식에 대한 이해에서 재귀를 어떻게 사용하여 속도를 향상시켰는지 알아보고 다른곳에 적용하기 위해

## 퀵정렬만의 특징

- 일반적인 시나리오에서 월등히 빠르다
- `분할` 이라는 개념 사용

### 분할

- 배열에서 하나를 정한 후(기준, 또는 피벗), 그 기준보다 작은 수는 왼쪽, 큰 수는 오른쪽으로 보내는 작업

```kotlin
class SortableArray {
    ...
    fun partition(array: IntArray, start: Int, end: Int): IntArray {
        val pivotIdx = end

        var leftIdx = 0
        var rightIdx = end - 1

        while (true) {
            while (array[leftIdx] < array[pivotIdx]) {
                leftIdx++
            }

            while (array[rightIdx] > array[pivotIdx]) {
                rightIdx++
            }

            if (leftIdx >= rightIdx) {
                break
            } else {
                val temp = array[leftIdx]
                array[leftIdx] = array[rightIdx]
                array[rightIdx] = temp
                //바뀌었으니깐 새 시작을 위해 왼쪽을 한칸 이동
                leftIdx++
            }
        }

        val temp = array[pivotIdx]
        array[pivotIdx] = array[leftIdx]
        array[leftIdx] = pivotIdx

        return leftIdx
    }
    ...
}

```

### Quick sort

- 퀵 정렬 = 분할 + 재귀
- 퀵 정렬은 배열을 작은 단위(sub array)로 쪼개서 위 정렬을 반복한다.

```kotlin

fun sort(array: IntArray, ) {
    val pivot = array.lastIndex
    val leftIndex = partition(array, )
}

```