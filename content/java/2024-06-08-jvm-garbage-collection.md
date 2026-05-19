+++
title = "JVM 가비지 컬렉션 — 메모리는 누가 치우는가"
date = "2024-06-08"
description = "JVM이 더 이상 참조되지 않는 객체를 자동으로 찾아 메모리를 회수하는 과정이 가비지 컬렉션이다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> JVM이 더 이상 참조되지 않는 객체를 자동으로 찾아 메모리를 회수하는 과정이 가비지 컬렉션이다.

---

## 가비지 컬렉션을 왜 쓰는지 감 잡기

C 언어에서는 `malloc`으로 메모리를 직접 빌리고 `free`로 직접 반납해야 한다. 반납을 잊으면 메모리 누수가 생기고, 이미 반납한 메모리를 다시 쓰면 프로그램이 죽는다. Java는 이 부담을 JVM이 대신 진다. 개발자가 객체를 `new`로 만들기만 하면, JVM이 주기적으로 "이 객체 아직 쓰나?" 를 확인하고 안 쓰는 것을 치운다.

문제는 이 청소 과정이 애플리케이션을 잠깐 멈추기도 한다는 것이다. 그래서 GC가 언제, 어떻게 동작하는지 알아야 성능 문제를 진단할 수 있다.

`핵심 흐름: 객체 생성 → Young Generation 저장 → Minor GC → Old Generation 이동 → Major GC → 메모리 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Heap | JVM이 객체를 쌓아두는 큰 창고. GC가 이 창고를 청소한다. |
| Young Generation | 막 태어난 객체들이 사는 공간. 청소 주기가 잦다. |
| Old Generation | Young에서 살아남은 오래된 객체들이 옮겨 오는 공간. |
| Minor GC | Young Generation만 청소하는 빠른 GC. |
| Major GC | Old Generation까지 청소하는 무거운 GC. 앱이 더 오래 멈춘다. |

## 예를 들어 설명하면

```java
public class Example {
    public static void main(String[] args) {
        String greeting = new String("Hello, World!");
        // greeting은 Eden(Young) 영역에 생성된다.
        // 이 메서드가 끝나면 greeting을 참조하는 변수가 없어진다.
        // 다음 Minor GC 때 수거 대상이 된다.
    }
}
```

객체가 Minor GC를 여러 번 버티면 Old Generation으로 승격된다. Old가 꽉 차면 Major GC(Full GC)가 발생하고 애플리케이션 전체가 잠시 멈춘다(Stop-the-World). 이 멈춤이 응답 지연으로 이어지기 때문에 GC 튜닝은 대부분 Old GC 빈도를 줄이는 방향으로 진행된다.

## 이 단계에서 중요한 판단 기준

GC 로그(`-verbose:gc`)에서 Major GC 빈도와 소요 시간이 올라가고 있다면, 힙 크기 조정 또는 GC 알고리즘 변경을 검토할 타이밍이다.

## 한 줄 요약 — 이것만 기억하면 된다

**JVM은 참조가 끊긴 객체를 Young → Old 세대 구조로 나눠 청소하며, Major GC가 성능에 직접 영향을 준다.**

## 나중에 더 깊게 들어가면

- G1 GC, ZGC, Shenandoah 등 최신 GC 알고리즘 비교
- Stop-the-World를 최소화하는 Concurrent Mark-Sweep 원리
- JVM 힙 크기 옵션(`-Xms`, `-Xmx`)과 GC 튜닝 전략

---

**원본:** [JVM Garbage Collection — https://memoryhub.tistory.com/223](https://memoryhub.tistory.com/223)
