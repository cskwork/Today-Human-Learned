# Spring Batch 설정을 application.properties로 관리하는 법

> **TL;DR**
> `application.properties` 하나에 Spring Batch의 DB 연결, 실행 여부, 재시도 횟수를 모아두면 코드 수정 없이 환경마다 배치 동작을 바꿀 수 있다.

---

## Spring Batch 설정을 왜 파일로 빼는지 감 잡기

Spring Batch는 대량 데이터를 반복 처리하는 작업(예: 매일 밤 거래 내역 집계, 주기적인 파일 적재)을 구조화하는 프레임워크다. 배치 잡이 복잡해질수록 DB 접속 정보, 재시도 횟수, 스레드 수 같은 값들이 코드 곳곳에 흩어지기 쉽다. 이를 `application.properties` 한 곳에 모으면 운영팀이 코드 빌드 없이 설정만 교체해 동작을 바꿀 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: application.properties 로드 → Spring Boot 자동 설정 → Batch Job 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Job | 배치 작업의 단위. "오늘 치 매출 합산" 같은 하나의 업무 흐름 |
| Step | Job 안에서 실제로 읽고-처리하고-쓰는 한 단계 |
| Chunk | Step이 한 번에 처리하는 데이터 묶음 크기 |
| initialize-schema | 앱 시작 시 Batch 메타 테이블을 자동 생성할지 여부 |
| retry-limit | 처리 실패 시 재시도 최대 횟수. 초과하면 Job 자체를 실패 처리 |

## 예를 들어 설명하면

실제 운영에서 자주 쓰는 최소 설정 조합이다.

```properties
# DB 연결 — Batch 메타데이터를 저장할 DB
spring.datasource.url=jdbc:mysql://localhost:3306/batch_db
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Batch 실행 제어
spring.batch.job.enabled=true          # false 로 바꾸면 앱 기동 시 자동 실행 안 함
spring.batch.initialize-schema=always  # 운영에서는 never 권장
spring.batch.table-prefix=BATCH_

# 재시도 / 스킵 한도
spring.batch.job.repository.retry-limit=3
spring.batch.job.repository.skip-limit=5

# 커스텀 — 코드에서 @Value 로 주입해서 사용
batch.custom.chunk.size=10
batch.custom.thread.pool.size=5
```

`initialize-schema=always` 는 로컬/개발에서만 쓴다. 운영 DB에 쓰면 기존 메타 테이블이 덮어써질 수 있다.

## 이 단계에서 중요한 판단 기준

운영 환경에서는 `spring.batch.initialize-schema=never`, `spring.batch.job.enabled=false`가 기본값이 되어야 한다 — 잡은 스케줄러(Quartz, cron)가 명시적으로 호출해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**환경별로 달라지는 값은 모두 `application.properties`에 두고, 코드에는 `@Value`나 `@ConfigurationProperties`로만 참조한다.**

## 나중에 더 깊게 들어가면

- Spring Profiles(`application-prod.properties`)로 환경별 설정 분리하기
- `@ConfigurationProperties`를 써서 커스텀 설정을 타입 안전하게 바인딩하기
- Spring Cloud Config Server로 설정을 외부 저장소에서 동적으로 가져오기

---

**원본:** [Managing Spring Batch Settings in `application.properties`](https://memoryhub.tistory.com/153)
