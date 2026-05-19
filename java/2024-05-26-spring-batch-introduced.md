# Spring Batch 입문 — 대량 데이터를 묶음으로 처리하는 프레임워크

> **TL;DR**
> Spring Batch는 대량 데이터를 Job-Step-Chunk 구조로 나눠 처리하는 프레임워크로, 실패 지점부터 재시작과 트랜잭션 관리를 자동으로 처리해준다.

---

## Spring Batch를 왜 쓰는지 감 잡기

하루에 수백만 건의 거래 내역을 정산하거나, 대용량 CSV 파일을 데이터베이스에 적재하는 작업을 생각해보자. 이런 작업을 단순 반복문으로 처리하면 중간에 오류가 나면 처음부터 다시 시작해야 하고, 트랜잭션 경계도 직접 관리해야 한다. Spring Batch는 이 문제를 Job, Step, Chunk 세 가지 개념으로 구조화해서 해결한다. Spring Framework 위에서 동작하므로 의존성 주입과 트랜잭션 관리를 그대로 활용한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Job 정의 → Step 구성 → (ItemReader → ItemProcessor → ItemWriter) 반복`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Job | 하나의 배치 작업 전체를 묶는 단위. 여러 Step으로 구성된다. |
| Step | Job 안의 한 단계. 읽고-처리하고-쓰는 흐름을 하나씩 담당한다. |
| Chunk | 데이터를 한 번에 처리하는 묶음 크기. 예: 1000건씩 처리. |
| ItemReader | 외부 소스(파일, DB 등)에서 데이터를 하나씩 읽어오는 역할 |
| ItemWriter | 처리된 데이터를 목적지(DB, 파일 등)에 저장하는 역할 |

## 예를 들어 설명하면

CSV 파일에서 주문 데이터를 읽어 상태를 갱신한 뒤 데이터베이스에 쓰는 Step이다.

```java
@Bean
public Step step1(StepBuilderFactory stepBuilderFactory) {
    return stepBuilderFactory.get("step1")
        .<Order, Order>chunk(10)   // 10건씩 묶어서 처리
        .reader(reader())          // CSV에서 읽기
        .processor(processor())    // 할인 계산, 상태 업데이트
        .writer(writer(null))      // DB에 쓰기
        .build();
}
```

`chunk(10)`은 ItemReader가 10건을 읽을 때마다 ItemProcessor와 ItemWriter를 호출하고, 10건 단위로 트랜잭션을 커밋한다. 중간에 오류가 나도 최대 10건만 롤백된다.

## 이 단계에서 중요한 판단 기준

chunk 크기가 너무 작으면 트랜잭션 오버헤드가 커지고, 너무 크면 메모리를 많이 쓴다. 일반적으로 100~1000 사이에서 성능 테스트로 결정한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Spring Batch는 대량 데이터를 Job-Step-Chunk로 나눠 처리하며, 실패 복구와 트랜잭션을 프레임워크가 대신 관리한다.**

## 나중에 더 깊게 들어가면

- JobRepository와 메타 테이블: 배치 실행 이력을 저장하고 재시작 지점을 기록하는 방법
- ItemProcessor 체이닝: 여러 처리 단계를 연결하는 CompositeItemProcessor
- 파티셔닝과 멀티스레드 Step: 대용량 처리를 병렬화하는 방법

---

**원본:** [Spring Batch Introduced — https://memoryhub.tistory.com/87](https://memoryhub.tistory.com/87)
