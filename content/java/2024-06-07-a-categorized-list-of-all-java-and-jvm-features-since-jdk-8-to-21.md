+++
title = "JDK 8 ~ 21 핵심 변화 — Java가 어떻게 달라졌는지 한눈에 보기"
date = "2024-06-07"
description = "Java는 JDK 8의 람다·스트림을 시작으로, 매 버전마다 언어 표현력·라이브러리·JVM 성능을 조금씩 발전시켜 왔다. 어디서 무엇이 생겼는지 알아야 레거시 코드를 읽고 최신 문법을 제대로 쓸 수 있다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Java는 JDK 8의 람다·스트림을 시작으로, 매 버전마다 언어 표현력·라이브러리·JVM 성능을 조금씩 발전시켜 왔다. 어디서 무엇이 생겼는지 알아야 레거시 코드를 읽고 최신 문법을 제대로 쓸 수 있다.

---

## 왜 버전 변화를 파악해야 하는지 감 잡기

Java 팀에 합류하면 JDK 8 코드와 JDK 17 코드가 같은 프로젝트에 섞여 있는 경우가 많다. 어떤 문법이 어느 버전부터 쓸 수 있는지 모르면, 새 문법을 쓰다 컴파일 오류를 만나거나 이미 더 나은 방법이 있는데 옛날 방식을 그대로 따라 쓰게 된다.

변화는 세 층위에서 일어난다.

`핵심 흐름: 언어(문법 변화) → 라이브러리(API 추가) → JVM(런타임 성능·GC 개선)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 람다 표현식 (JDK 8) | 메서드를 값처럼 다루는 문법. `(x) -> x * 2` 형태. 익명 클래스를 짧게 대체 |
| var (JDK 10) | 컴파일러가 타입을 추론하도록 맡기는 키워드. 타입을 생략하는 게 아니라 컴파일러가 대신 결정 |
| Record (JDK 16 정식) | 불변 데이터 보관용 클래스. getter·equals·hashCode·toString을 자동 생성 |
| Sealed Class (JDK 17 정식) | 상속 가능한 클래스를 명시적으로 제한. 패턴 매칭과 함께 쓰면 강력 |
| Virtual Thread (JDK 21) | JVM이 관리하는 초경량 스레드. OS 스레드 수백 개로 수백만 동시 작업 처리 가능 |

## 예를 들어 설명하면

버전별 주요 추가 기능을 카테고리로 묶으면 다음과 같다.

| 버전 | 언어 | 라이브러리 | JVM |
|---|---|---|---|
| 8 | 람다, 메서드 참조, 디폴트 메서드 | Stream API, Optional, 새 날짜 API | PermGen 제거 |
| 9 | private 인터페이스 메서드 | 모듈 시스템(Jigsaw), JShell | AOT 컴파일 |
| 10 | `var` 타입 추론 | `Collectors.toUnmodifiableList` | 클래스 데이터 공유 개선 |
| 11 | 람다 파라미터에 `var` | HttpClient API, 문자열 메서드 강화 | Epsilon GC (no-op) |
| 14 | Records (preview), instanceof 패턴 매칭 (preview) | 유용한 NullPointerException 메시지 | NUMA 메모리 최적화 |
| 16 | Records 정식, instanceof 패턴 매칭 정식 | Vector API (incubator) | ZGC 스레드 스택 처리 개선 |
| 17 | Sealed Classes 정식 | 향상된 난수 생성기 | 역직렬화 필터 강화 |
| 21 | String Templates (preview) | Sequenced Collections | Virtual Threads 정식, Generational ZGC |

## 이 단계에서 중요한 판단 기준

새 프로젝트라면 JDK 21(LTS)을 기준으로 시작하고, 기존 프로젝트를 업그레이드할 때는 JDK 11 → 17 → 21 순서의 LTS 단계를 따른다. Preview 기능은 다음 버전에서 API가 바뀔 수 있으므로 프로덕션 코드에서는 정식 릴리스 후 사용한다.

## 한 줄 요약 — 이것만 기억하면 된다

**JDK 8의 람다·스트림이 Java를 함수형 언어처럼 쓸 수 있게 만들었다면, JDK 16~21의 Record·Sealed Class·Virtual Thread는 코드 간결성과 대규모 동시성 처리를 한 단계 끌어올렸다.**

## 나중에 더 깊게 들어가면

- 패턴 매칭 for switch (JDK 21 정식): sealed class와 결합해 타입별 분기를 안전하게 처리하는 방법
- Virtual Thread와 기존 스레드 풀의 차이: Loom 프로젝트가 해결한 blocking I/O 병목 문제
- 모듈 시스템(Project Jigsaw): 대형 애플리케이션에서 의존성 캡슐화와 런타임 이미지 최소화

---

**원본:** [A categorized list of all Java and JVM features since JDK 8 to 21 — https://memoryhub.tistory.com/204](https://memoryhub.tistory.com/204)
