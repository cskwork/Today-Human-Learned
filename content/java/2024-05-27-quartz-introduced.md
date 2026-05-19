+++
title = "Quartz — Java 애플리케이션의 작업 스케줄러"
date = "2024-05-27"
description = "Quartz는 Java 앱에서 \"언제, 얼마나 자주\" 어떤 작업을 실행할지 선언적으로 정의하는 스케줄링 라이브러리다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Quartz는 Java 앱에서 "언제, 얼마나 자주" 어떤 작업을 실행할지 선언적으로 정의하는 스케줄링 라이브러리다.

---

## Quartz를 왜 쓰는지 감 잡기

매일 새벽 2시에 DB 백업, 10분마다 외부 API 동기화, 매월 1일 정산 처리 — 이런 정기 작업을 직접 Thread와 Timer로 구현하면 코드가 복잡해지고 장애 시 재시도 처리가 어렵다. Quartz는 이 모든 것을 선언적 API로 처리한다. 어떤 작업(Job)을 어떤 조건(Trigger)에 실행할지 등록하면, Scheduler가 알아서 관리한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Job 정의 → JobDetail로 포장 → Trigger 조건 설정 → Scheduler에 등록 → 자동 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Scheduler | 모든 작업의 실행을 총괄하는 관리자 |
| Job | 실제로 실행될 작업 로직을 담은 클래스 |
| JobDetail | Job의 이름, 그룹, 설정을 담은 메타데이터 컨테이너 |
| Trigger | 작업을 언제 실행할지 조건을 정의하는 설정 (시간, 주기 등) |
| JobStore | Job과 Trigger 상태를 저장하는 저장소 (메모리 또는 DB 기반) |

## 예를 들어 설명하면

10초마다 특정 작업을 실행하는 Quartz 예시다.

```java
// 1. Job 구현
public class MyJob implements Job {
    public void execute(JobExecutionContext context) throws JobExecutionException {
        System.out.println("Job is executing!");
    }
}

// 2. Scheduler 생성 및 작업 등록
SchedulerFactory schedulerFactory = new StdSchedulerFactory();
Scheduler scheduler = schedulerFactory.getScheduler();

JobDetail jobDetail = JobBuilder.newJob(MyJob.class)
    .withIdentity("myJob", "group1")
    .build();

Trigger trigger = TriggerBuilder.newTrigger()
    .withIdentity("myTrigger", "group1")
    .startNow()
    .withSchedule(SimpleScheduleBuilder.simpleSchedule()
        .withIntervalInSeconds(10)
        .repeatForever())
    .build();

scheduler.scheduleJob(jobDetail, trigger);
scheduler.start();
```

Trigger 타입을 CronTrigger로 바꾸면 `0 0 2 * * ?` 같은 cron 표현식으로 정밀한 스케줄도 지정할 수 있다.

## 이 단계에서 중요한 판단 기준

단순 `@Scheduled`(Spring)로 충분한 경우와 달리, 클러스터 환경에서 중복 실행을 막거나 장애 후 미실행 작업을 재개해야 할 때 Quartz의 JDBC JobStore를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Quartz는 Job(무엇을) + Trigger(언제) + Scheduler(관리)의 세 요소로 Java 정기 작업을 선언적으로 운영한다.**

## 나중에 더 깊게 들어가면

- CronTrigger: cron 표현식으로 복잡한 실행 주기 정의하기
- JDBC JobStore: 서버 재시작 후에도 스케줄 상태를 유지하는 DB 기반 저장소
- Quartz 클러스터링: 여러 인스턴스 간 Job 중복 실행 방지 설정

---

**원본:** [Quartz Introduced — https://memoryhub.tistory.com/127](https://memoryhub.tistory.com/127)
