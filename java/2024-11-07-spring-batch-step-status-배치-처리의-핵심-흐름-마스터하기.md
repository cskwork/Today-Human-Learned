# Spring Batch Step과 Status — 배치 흐름 제어의 기초

> **TL;DR**
> Spring Batch의 Job은 여러 Step으로 나뉘고, 각 Step은 BatchStatus로 실행 상태를 기록한다. Status 값에 따라 다음 Step으로 갈지, 복구 경로로 갈지를 선언적으로 제어할 수 있다.

---

## Spring Batch Step을 왜 쓰는지 감 잡기

수십만 건의 거래 데이터를 매일 밤 처리하는 배치 작업이 있다고 하자. 이것을 하나의 거대한 메서드로 짜면 중간에 실패했을 때 처음부터 다시 시작해야 한다. Step으로 나누면 실패한 지점부터 재시작할 수 있고, 각 Step은 자신의 트랜잭션 경계를 가져 데이터 무결성도 보장된다.

공장 조립 라인으로 비유하면 이해하기 쉽다. 각 작업 스테이션이 Step이고, 스테이션마다 신호등(Status)이 있다. 초록불이면 다음 스테이션으로 넘어가고, 빨간불이면 복구 경로로 전환된다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Job 실행 → Step1 (read→process→write) → Step2 → ... → Job 완료`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Job | 배치 작업 전체. 하나 이상의 Step으로 구성된다 |
| Step | Job 안의 실행 단위. 각자 트랜잭션을 관리하고 상태를 기록한다 |
| BatchStatus | Step/Job의 실행 상태를 나타내는 열거형 (COMPLETED, FAILED 등) |
| ExitStatus | Step 완료 후의 결과 코드. BatchStatus와 달리 개발자가 커스터마이징 가능 |
| Chunk | 데이터를 N건씩 묶어서 한 트랜잭션으로 처리하는 단위 |

## 예를 들어 설명하면

일일 결산 배치를 5단계 Step으로 구성한 예시다.

```java
@Bean
public Job dailySettlementJob(JobRepository jobRepository) {
    return new JobBuilder("dailySettlementJob", jobRepository)
        .start(dataExtractionStep())      // 거래 데이터 추출
        .next(dataTransformationStep())   // 회계 포맷 변환
        .next(reportGenerationStep())     // 보고서 생성
        .next(emailSendingStep())         // 이메일 발송 (Tasklet)
        .next(tempFileCleanupStep())      // 임시 파일 정리 (Tasklet)
        .build();
}

// Chunk-oriented Step: 100건씩 읽고 처리하고 쓴다
@Bean
public Step dataExtractionStep(JobRepository jobRepository, PlatformTransactionManager tm) {
    return new StepBuilder("dataExtractionStep", jobRepository)
        .<Transaction, Transaction>chunk(100, tm)
        .reader(transactionReader())
        .writer(transactionWriter())
        .build();
}
```

Step 실행 결과에 따라 흐름을 분기할 때는 `on()`을 쓴다.

```java
@Bean
public Job conditionalJob(JobRepository jobRepository) {
    return new JobBuilder("conditionalJob", jobRepository)
        .start(firstStep())
        .on("COMPLETED").to(successStep())
        .from(firstStep()).on("FAILED").to(recoveryStep())
        .end()
        .build();
}
```

BatchStatus 값 전체는 다음과 같다.

| 상태 | 의미 |
|---|---|
| COMPLETED | 성공적으로 완료 |
| STARTED / STARTING | 실행 중 또는 시작 준비 |
| STOPPED | 중지됨 (재시작 가능) |
| FAILED | 오류로 실패 |
| ABANDONED | 재시작 시 건너뜀 |
| UNKNOWN | 비정상 종료로 상태 불명확 |

## 이 단계에서 중요한 판단 기준

Chunk 크기는 너무 작으면 트랜잭션 오버헤드가 커지고, 너무 크면 실패 시 롤백 범위가 넓어진다. 데이터 특성에 따라 다르지만 10~100 사이에서 시작해 측정 후 조정하라.

## 한 줄 요약 — 이것만 기억하면 된다

**Step은 배치 작업의 독립 실행 단위이고, BatchStatus는 각 Step의 결과를 기록해 재시작과 조건부 흐름을 가능하게 한다.**

## 나중에 더 깊게 들어가면

- `StepExecutionListener` — Step 실행 전후에 로직을 끼워 넣는 방법
- `faultTolerant()` — 특정 예외는 건너뛰고(skip), 일시적 오류는 재시도(retry)하는 내결함성 설정
- JobParameters와 JobInstance — 같은 Job을 날짜별로 구분해서 실행하는 방법

---

**원본:** [Spring Batch Step & Status - 배치 처리의 핵심 흐름 마스터하기](https://memoryhub.tistory.com/369)
