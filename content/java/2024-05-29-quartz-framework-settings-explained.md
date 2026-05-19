+++
title = "Quartz 스케줄러 설정 이해하기"
date = "2024-05-29"
description = "Quartz는 네 가지 설정 블록(스케줄러, 스레드풀, 잡 스토어, 플러그인)으로 구성되고, 이 값들을 `quartz.properties`에 모아두면 스케줄링 동작 전체를 제어할 수 있다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Quartz는 네 가지 설정 블록(스케줄러, 스레드풀, 잡 스토어, 플러그인)으로 구성되고, 이 값들을 `quartz.properties`에 모아두면 스케줄링 동작 전체를 제어할 수 있다.

---

## Quartz를 왜 쓰는지 감 잡기

"매일 오전 2시에 정산 잡을 돌려라", "30분마다 외부 API를 폴링해라" 같은 요구사항은 OS cron으로도 가능하지만, 잡 실행 이력 저장, 클러스터 환경에서의 중복 실행 방지, 실패 시 재실행 같은 기능이 필요하면 cron만으로는 부족하다. Quartz는 이런 기업용 스케줄링 요구사항을 Java 내부에서 처리한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 트리거 발화 → 스레드풀에서 스레드 획득 → 잡 실행 → 결과를 잡 스토어에 저장`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Scheduler | 잡과 트리거를 등록·관리하는 중앙 관리자 |
| Trigger | "언제 실행할지" 규칙. cron 표현식이나 반복 간격으로 설정 |
| Job | 실제로 실행할 코드가 담긴 단위. `execute()` 메서드 하나로 구성 |
| JobStore | 잡·트리거 정보를 메모리 또는 DB에 저장하는 저장소 |
| Thread Pool | 잡을 병렬로 실행하기 위한 스레드 집합. 크기가 동시 실행 가능한 잡 수를 결정 |

## 예를 들어 설명하면

JDBC 잡 스토어를 쓰는 클러스터 환경 설정 전체 예시다.

```properties
# 스케줄러 기본
org.quartz.scheduler.instanceName=MyScheduler
org.quartz.scheduler.instanceId=AUTO

# 스레드풀 — threadCount 가 동시 실행 가능한 잡 수의 상한
org.quartz.threadPool.class=org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount=10
org.quartz.threadPool.threadPriority=5

# 잡 스토어 — DB에 저장해야 클러스터링 가능
org.quartz.jobStore.class=org.quartz.impl.jdbcjobstore.JobStoreTX
org.quartz.jobStore.driverDelegateClass=org.quartz.impl.jdbcjobstore.StdJDBCDelegate
org.quartz.jobStore.dataSource=myDS
org.quartz.jobStore.tablePrefix=QRTZ_
org.quartz.jobStore.isClustered=true

# 플러그인 — 실행 이력 로깅 + 안전한 종료
org.quartz.plugin.triggHistory.class=org.quartz.plugins.history.LoggingTriggerHistoryPlugin
org.quartz.plugin.shutdownhook.class=org.quartz.plugins.management.ShutdownHookPlugin
org.quartz.plugin.shutdownhook.cleanShutdown=true
```

클러스터 환경이 아니라면 `jobStore.class`를 `RAMJobStore`로 바꾸면 DB 없이 메모리만으로 동작한다. 단, 앱 재시작 시 스케줄 정보가 사라진다.

## 이 단계에서 중요한 판단 기준

서버가 두 대 이상이거나 잡 이력을 영구 보존해야 한다면 `JobStoreTX`(DB 저장) + `isClustered=true`를, 단일 서버 + 이력 불필요면 `RAMJobStore`를 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Quartz 설정은 "언제(트리거), 무엇을(잡), 몇 개 동시에(스레드풀), 어디에 기록할지(잡 스토어)" 네 가지 질문에 답하는 것이다.**

## 나중에 더 깊게 들어가면

- Spring Boot `spring-boot-starter-quartz`와의 통합 방식
- CronTrigger vs SimpleTrigger 선택 기준
- 클러스터 환경에서 `instanceId=AUTO` 동작 원리와 충돌 방지 메커니즘

---

**원본:** [Quartz Framework Settings Explained](https://memoryhub.tistory.com/154)
