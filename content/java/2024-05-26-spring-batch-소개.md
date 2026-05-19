+++
title = "Spring Batch 소개 — 대량 데이터를 묶음으로 처리하는 프레임워크 (한국어판)"
date = "2024-05-26"
description = "Spring Batch는 Job-Step-Chunk 구조로 대량 데이터를 효율적으로 처리하며, 실패 복구와 트랜잭션을 프레임워크가 자동으로 관리한다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Spring Batch는 Job-Step-Chunk 구조로 대량 데이터를 효율적으로 처리하며, 실패 복구와 트랜잭션을 프레임워크가 자동으로 관리한다.

---

## Spring Batch를 왜 쓰는지 감 잡기

백만 건의 주문 내역을 매일 밤 정산하거나, 수십만 행짜리 CSV를 데이터베이스에 적재하는 작업이 있다고 가정하자. 단순 반복문으로 처리하면 중간에 오류가 생겼을 때 어디서 멈췄는지 추적하기 어렵고, 전체를 다시 실행해야 할 수도 있다. Spring Batch는 이 문제를 Job, Step, Chunk 세 층위로 구조화해서 해결한다. Spring Framework를 기반으로 동작하므로 의존성 주입, 트랜잭션 관리 같은 기능을 그대로 사용할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Job 정의 → Step 설계 → (ItemReader → ItemProcessor → ItemWriter) 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Job | 배치 작업 전체를 묶는 가장 큰 단위. 여러 Step으로 구성된다. |
| Step | Job 안의 처리 단계. 읽기-처리-쓰기를 하나씩 담당한다. |
| Chunk 처리 | 데이터를 N건씩 묶어 처리하는 방식. N건 단위로 커밋한다. |
| ItemProcessor | 읽어온 데이터를 가공하는 단계. 필터링, 계산, 변환을 담당한다. |
| ItemWriter | 처리 결과를 DB나 파일 등 목적지에 저장하는 단계 |

## 예를 들어 설명하면

CSV에서 주문을 읽어 상태를 갱신한 뒤 데이터베이스에 저장하는 흐름이다.

```java
@Bean
public Step step1(StepBuilderFactory stepBuilderFactory) {
    return stepBuilderFactory.get("step1")
        .<Order, Order>chunk(10)   // 10건씩 묶어 처리
        .reader(reader())          // CSV 파일에서 읽기
        .processor(processor())    // 할인 계산, 상태 갱신
        .writer(writer(null))      // DB에 저장
        .build();
}
```

`chunk(10)`은 10건을 읽을 때마다 처리와 저장을 실행하고 트랜잭션을 커밋한다. 오류가 나도 최대 10건 범위만 롤백된다. 이것이 단순 반복문보다 Spring Batch가 강력한 이유다.

## 이 단계에서 중요한 판단 기준

Chunk 크기는 성능과 복구 범위 사이의 균형이다. 크면 처리 속도는 빠르지만 오류 시 재처리 범위도 커진다. 100~1000 사이에서 테스트하며 결정한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Spring Batch는 대량 데이터를 Job-Step-Chunk로 나눠 안전하게 처리하고, 중간 실패 시 해당 구간만 재처리할 수 있다.**

## 나중에 더 깊게 들어가면

- JobRepository: 배치 실행 이력을 저장하고 재시작 지점을 기록하는 메타 테이블
- Tasklet vs Chunk: 단순 단일 작업은 Tasklet, 대량 처리는 Chunk 지향 방식을 선택하는 기준
- 멀티스레드 Step과 파티셔닝: 병렬 처리로 배치 속도를 높이는 방법

---

**원본:** [Spring Batch 소개 — https://memoryhub.tistory.com/88](https://memoryhub.tistory.com/88)
