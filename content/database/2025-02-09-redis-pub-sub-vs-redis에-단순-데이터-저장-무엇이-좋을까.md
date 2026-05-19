+++
title = "Redis Pub/Sub vs. 자료구조 저장 — 언제 무엇을 고를까"
date = "2025-02-09"
description = "Pub/Sub은 실시간 알림에 쓰고, Set/List 같은 자료구조는 나중에 조회할 데이터 누적에 쓴다 — 둘은 목적이 다르므로 상황에 따라 병행하는 것이 자연스럽다."
tags = ["database"]
categories = ["database"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Pub/Sub은 실시간 알림에 쓰고, Set/List 같은 자료구조는 나중에 조회할 데이터 누적에 쓴다 — 둘은 목적이 다르므로 상황에 따라 병행하는 것이 자연스럽다.

---

## 두 방식을 왜 구분해야 하는지 감 잡기

Redis 하나로 "에러 알림"과 "에러 로그 보관"을 동시에 하려 할 때 혼란이 생긴다. Pub/Sub에 메시지를 발행하면 구독자가 실시간으로 받을 수 있다. 하지만 구독자가 오프라인이면 그 메시지는 사라진다. 반면 Set에 에러를 저장하면 나중에 꺼내볼 수 있지만, 저장했다고 누군가에게 자동으로 알림이 가지는 않는다. 두 방식은 "알림"과 "저장"이라는 서로 다른 문제를 푼다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름(Pub/Sub): 이벤트 발생 → PUBLISH → 구독 중인 서버가 즉시 수신`
`핵심 흐름(자료구조): 이벤트 발생 → SADD/LPUSH → 나중에 SMEMBERS/LRANGE로 조회`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PUBLISH | Pub/Sub 채널에 메시지를 던지는 명령어. 저장되지 않는다. |
| SUBSCRIBE | 채널을 실시간으로 수신 대기 상태로 만드는 명령어. |
| SADD | Set에 값을 추가하는 명령어. 중복은 자동으로 무시된다. |
| LPUSH | List의 앞쪽에 값을 추가하는 명령어. 로그 큐로 자주 쓴다. |
| 메시지 유실 | Pub/Sub에서 구독자가 없을 때 발행된 메시지는 복구 불가능하다. |

## 예를 들어 설명하면

채팅 에러를 처리하는 두 가지 방식을 Spring Boot 코드로 비교한다.

```java
// Pub/Sub: 에러 발생 즉시 모니터링 서버에 알림
redisTemplate.convertAndSend("chatErrorChannel", errorMessage);

// 자료구조: 에러를 Set에 누적해 사후 분석용으로 보관
stringRedisTemplate.opsForSet().add("chat_error_logs", errorMessage);
```

두 방식의 핵심 차이:

| 기준 | Pub/Sub | Set / List 저장 |
|---|---|---|
| 실시간 알림 | 가능 | 불가 (폴링 필요) |
| 메시지 보관 | 불가 (휘발) | 가능 |
| 구현 난이도 | 중간 (Listener 필요) | 낮음 |
| 사후 분석 | 어려움 | 쉬움 |

## 이 단계에서 중요한 판단 기준

메시지를 놓치면 안 되는 상황이라면 Pub/Sub 단독은 위험하다 — 실시간 알림은 Pub/Sub으로, 보관과 분석은 자료구조나 DB로 이중화하는 하이브리드 방식이 실무 표준이다.

## 한 줄 요약 — 이것만 기억하면 된다

**Pub/Sub은 실시간 알림용이고 자료구조 저장은 데이터 보관용이므로, 중요한 이벤트는 둘 다 써서 실시간 알림과 사후 분석을 함께 확보한다.**

## 나중에 더 깊게 들어가면

- Redis Stream: Pub/Sub의 실시간성과 자료구조의 영속성을 함께 제공하는 상위 대안
- Consumer Group으로 여러 서버가 같은 Stream을 분산 처리하기
- 메시지 유실이 절대 안 되는 요건에서 Kafka와의 역할 비교

---

**원본:** [Redis Pub/Sub vs. Redis에 단순 데이터 저장, 무엇이 좋을까? — https://memoryhub.tistory.com/445](https://memoryhub.tistory.com/445)
