---
layout: default
title: Garbage Collection
parent: Kotlin
nav_order: 1
---

# Garbage Collection

---

## GC는 무엇인가

- 힙 영역에 있는 unreachable 메모리를 청소하는 역할
- 힙 영역?
    - 개발자가 직접 관리하는 메모리 영역
    - 개발자에 의해 메모리 공간이 동적으로 할당되고 해제

## Unreachable 메모리의 정의

- 각 객체들의 상호 참조여부를 기반으로 판단하는게 아닌
- `GC root에서 이 객체에 접근할수있냐 없냐로 reachable, unreachable 판단`

## GC root의 예시

- 실행중인 스레드
- 정적변수
- 로컬변수
- 힙 영역내 다른 객체의 의한 참조
- JNI 레퍼런스

## Mark and Sweep

- GC 의 대표적인 알고리즘 중에 하나
- Mark
    - 힙 영역에 할당된 객체들을 체크하며 GC root에서 참조된 객체들을 예의주시 하는 과정
- Sweep
    - 그 중 unreachable한 메모리를 수거하는 과정

## GC의 동작 방법

- 힙 영역의 구분: New Generation / Old Generation
- New Generation의 구분: Eden, Survivor 0, Survivor 1
- 힙 영역내에 GC 동작
    - 새롭게 할당되는 객체는 Eden 영역에 위치
    - Eden 영역이 꽉 차게 되면 -> GC 발생
    - GC 발생 후 살아남은 객체들은 Survivor 0영역으로 이동
        - 이때 살아 남았다는 의미의 age bit 값을 +1 살아남을때 마다 해준다.
    - Survivor 0 영역이 꽉차게 되면 -> GC 발생
        - 또 다시 살아남은 객체들은 Survivor 1 영역으로 이동 하고 Survivor 0 영역 클리어
    - 다시 새로운 객체들이 생성되고 꽉차게 되면 GC 발생후 Survivor 1 영역으로 이동
    - Survivor 1 영역도 꽉차게 되면 GC 후 Survivor 0 영역으로 객체 이동

- Old Generation
    - 이 영역으로 옮겨지는 객체들은 age bit 값에 따라 좌우된다.
    - 특정 age bit 값을 만족하는 객체들은 old generation 영역으로 옮긴다 -> Promotion
    - 특정 age bit 값은 설정 가능

## GC 종류

- Minor GC
    - New Generation에서 발생하는 GC
- Full GC(Major GC)
    - Old Generation에서 발생하는 GC
- 결국 GC는 메모리가 꽉차면 동작

## OOM

- 힙 영역에서 GC가 동작해도 수거할 객체가 없는데 새로운 객체를 힙 영역에 저장하려고 할때 발생

## 참조 유형 - GC가 동작할 때 우선 순위를 매길 수 있다면?

- Strong reference
    - new 키워드를 통해 생성되는 객체
    - GC의 수거 대상에서 제외
- Soft reference
    - 때에 따라 될 수도 안 될 수도 있는 객체
    - 메모리가 여유롭다면 안 될 수도있다.
- Weak reference
    - GC 동작 시 바로 바로 수거
    - reachable해도 바로 수거
    - 이 객체가 참조하는 하위 객체들도 바로 gc 대상

## GC를 계속 동작하게 만들면 어떻게 되나?

- 비효율
- Stop the world 현상
  - GC가 동작하면 관련 스레드를 제외한 나머지 스레드는 모두 멈춘다.
- 대용량을 다룰때, 혹은 빠른 처리를 해야할때 GC가 계속 동작한다면?? No

## 자바 11
- New / Old 로 구분하지 않고, 힙 영역을 Region 단위로 쪼개놓음
- 그래서 메모리가 꽉찬 Region만 GC하여 더 신속
- Mark and sweep에 더불어 사용하고 있는 메모리들을 모아서 더 효율적으로 메모리를 사용할 수 있게 Compaction이라는 단계도 추가



# 참조

[메모리 기본구조](http://www.tcpschool.com/c/c_memory_structure)

[Java Reference와 GC](https://d2.naver.com/helloworld/329631)

[IMDEV 2023 혼자서도 잘하는 메모리 청소부 쥐씨](https://www.youtube.com/watch?v=F4lWAWOTXyg)

[가비지 컬렉터(Garbage Collector)와 Mark & Sweep](https://imasoftwareengineer.tistory.com/103)
