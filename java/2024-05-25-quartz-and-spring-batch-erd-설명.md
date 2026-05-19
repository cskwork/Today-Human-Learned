# Quartz And Spring Batch ERD 설명

> **TL;DR**
> Quartz는 스케줄 정보를 DB에 저장해 여러 서버가 중복 없이 작업을 나눠 실행하고, Spring Batch는 각 배치 Job의 실행 이력을 자체 메타테이블에 기록한다.

---

## Quartz + Spring Batch를 왜 쓰는지 감 잡기

단일 서버에서 스케줄러를 메모리로 돌리면, 서버가 재시작되는 순간 예약된 작업이 전부 사라진다. 서버를 두 대 이상 띄우면 같은 작업이 동시에 두 번 실행되는 중복 실행 문제도 생긴다.

Quartz의 DB 클러스터 모드는 스케줄 정보를 공용 데이터베이스에 저장하고 DB 락(lock)을 통해 한 서버만 특정 트리거를 실행하도록 보장한다. Spring Batch는 이 실행된 작업의 결과와 이력을 별도 메타테이블에 남긴다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Trigger 발생 → Quartz가 DB 락 선점 → Spring Batch Job 실행 → 실행 이력 메타테이블에 저장`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Job (Quartz) | Quartz가 실행해야 할 작업 로직. 어떤 클래스를 실행할지 정의한다. |
| Trigger | Job을 언제 실행할지 정의하는 시간 정보. Cron 표현식이나 반복 간격을 담는다. |
| QRTZ_LOCKS | 클러스터 환경에서 여러 서버가 동시에 같은 Trigger를 실행하지 않도록 DB 수준에서 잠금을 거는 테이블. |
| BATCH_JOB_INSTANCE | Spring Batch에서 한 번의 Job 실행 단위를 식별하는 레코드. 같은 Job이라도 파라미터가 다르면 새 Instance가 생긴다. |
| BATCH_STEP_EXECUTION | Step(읽기-처리-쓰기) 단위로 처리 건수, 시작/종료 시간, 성공·실패 여부를 기록하는 테이블. |

## 예를 들어 설명하면

Spring Boot에서 Quartz를 DB 클러스터 모드로 설정하는 핵심 설정:

```yaml
spring:
  quartz:
    job-store-type: jdbc
    properties:
      org.quartz.jobStore.isClustered: true
      org.quartz.scheduler.instanceId: AUTO
```

`isClustered: true`를 설정하면 Quartz는 QRTZ_LOCKS 테이블에 락을 걸어 한 인스턴스만 Trigger를 점유하도록 보장한다. 나머지 인스턴스는 DB에서 이미 실행 중임을 확인하고 건너뛴다.

## 이 단계에서 중요한 판단 기준

Quartz 테이블(`QRTZ_*`)과 Spring Batch 테이블(`BATCH_*`)은 애플리케이션 기동 전에 DDL을 실행해 미리 만들어야 하며, 클러스터 서버 간 시스템 시간이 NTP로 동기화되어 있지 않으면 Trigger 실행 타이밍이 어긋날 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**Quartz는 "언제, 어느 서버에서 한 번만 실행할지"를 DB로 조율하고, Spring Batch는 "실행 결과와 이력"을 메타테이블에 남긴다.**

## 나중에 더 깊게 들어가면

- Quartz MisfirePolicy: 서버 다운 중 놓친 Trigger를 어떻게 처리할지 설정하는 방법
- Spring Batch JobRepository: 메타테이블 구조와 재시작(restart) 처리 메커니즘
- Quartz + Spring Batch 조합에서 멱등성(idempotency) 보장 전략

---

**원본:** [Quartz And Spring Batch ERD 설명 — https://memoryhub.tistory.com/51](https://memoryhub.tistory.com/51)
