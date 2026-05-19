+++
title = "Redis vs Amazon MQ vs SQS vs MSK — 언제 무엇을 써야 할까"
date = "2026-05-14"
description = "단순 비동기 작업은 SQS, 레거시 브로커 이전은 Amazon MQ, 대용량 이벤트 스트리밍은 MSK, 초고속 캐시·실시간 상태는 Redis. 메시지·캐시 서비스를 상황별로 정리한다."
tags = ["database", "aws", "redis", "kafka", "sqs"]
categories = ["database"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 단순 비동기 작업은 SQS, 레거시 브로커 이전은 Amazon MQ, 대용량 이벤트 스트리밍은 MSK, 초고속 캐시·실시간 상태는 Redis.

---

## 왜 네 가지를 비교해야 하는지

"비동기 작업이 필요해요"라는 한 줄 요구사항 뒤에는 보통 네 가지 후보가 들어 있다. 메시지 큐(SQS), 브로커(Amazon MQ), 이벤트 스트림(MSK), 초고속 캐시(Redis)다. 이름은 비슷해 보이지만 보장하는 것이 다르다. **하나를 잘못 고르면 6개월 뒤에 마이그레이션 비용이 코드보다 더 든다.**

## 네 서비스 한 표로 보기

| 서비스 | 한 줄 설명 | 강점 | 한계 | 대표 사용처 |
|--------|-----------|------|------|------------|
| **Redis** | 인메모리 초고속 저장소 | 마이크로초 단위 응답, 단순한 통합, 캐시·랭킹·세션·실시간 카운터 | 메시지 큐가 아님. Pub/Sub은 구독자 장애 시 메시지 손실 | 캐시, 세션 저장, 실시간 알림, 단순 이벤트 |
| **Amazon MQ** | AWS가 관리하는 RabbitMQ/ActiveMQ | 기존 RabbitMQ/ActiveMQ 이전이 쉬움, AMQP·MQTT·JMS 표준 지원, 풍부한 라우팅 | SQS보다 운영 복잡, 브로커 사이징·설정 이해 필요 | 레거시 시스템 이전, JMS 기반 시스템, 복잡한 라우팅 |
| **Amazon SQS** | 가장 단순하고 안정적인 큐 | 운영이 가장 쉬움, 완전 관리형, AZ 다중 고가용성, 시스템 간 결합도 낮춤 | 라우팅 제한, Standard는 중복 가능, FIFO는 처리량 제한 | 비동기 작업, 마이크로서비스 결합 해제, Lambda 트리거, 백그라운드 작업 |
| **Amazon MSK** | AWS가 관리하는 Apache Kafka | 대용량 이벤트 스트림, 여러 컨슈머가 이벤트 재생 가능, 실시간 분석 강함 | 개념 복잡도 최상위. topic, partition, consumer group, offset 이해 필요 | 대용량 이벤트, 데이터 레이크, 로그 집계, 실시간 분석, 다중 팀 이벤트 공유 |

## 상황별 빠른 선택

| 상황 | 의미 | 추천 |
|------|------|------|
| "주문을 비동기로 처리하고 싶다 (결제·알림·배송)" | 순차 작업 큐 처리 | **SQS** |
| "기존 RabbitMQ를 AWS로 옮기고 싶다" | 브로커 lift-and-shift | **Amazon MQ** |
| "클릭·로그·거래 이벤트를 모아 분석하고 싶다" | 보존 가능한 이벤트 하이웨이 | **MSK** |
| "로그인 세션·캐시·랭킹·카운터가 필요하다" | DB보다 빠른 임시 저장소 | **Redis** |
| "메시지 손실은 절대 안 된다" | 내구성 있는 큐/로그 필요 | **SQS / Amazon MQ / MSK** |
| "상태값을 1초 이하로 읽고 써야 한다" | 메시징보다 속도가 우선 | **Redis** |

## 판단 기준 5가지

1. **순서 보장이 필요한가** — 필요하면 SQS FIFO, MSK partition 단위 보장. Redis는 보장 안 함.
2. **재생(replay)이 필요한가** — 같은 이벤트를 여러 소비자가 다시 읽어야 하면 MSK가 유일한 정답.
3. **메시지 손실 허용 범위** — 손실 절대 불가면 SQS/MQ/MSK. 약간 허용되고 속도가 더 중요하면 Redis.
4. **운영 복잡도** — 가장 단순한 순서: SQS < Redis < MQ < MSK.
5. **표준 프로토콜 호환성** — JMS/AMQP/MQTT 클라이언트가 이미 있다면 Amazon MQ.

## 헷갈리기 쉬운 포인트

- **Redis Pub/Sub은 메시지 큐가 아니다.** 구독자가 죽으면 그 사이 발행된 메시지는 사라진다. 영속이 필요하면 Redis Streams를 쓰거나 SQS로 갈아타야 한다.
- **MSK는 "Kafka 알아야 쓸 수 있다."** topic, partition, consumer group, offset을 모르고 도입하면 안정성·비용·디버깅 모두 무너진다.
- **SQS Standard vs FIFO** — Standard는 중복·순서 어긋남이 가능하고, FIFO는 그룹당 처리량이 제한된다. 거래 시스템은 FIFO, 알림은 Standard가 보통.
- **Amazon MQ는 이전용에 가깝다.** 새 시스템을 처음부터 만든다면 SQS/MSK가 운영 부담이 낮다.

## 마치며

네 서비스는 경쟁 관계가 아니라 **레이어가 다른 인프라**다. 빠른 캐시는 Redis, 단순 비동기는 SQS, 복잡한 라우팅은 MQ, 재생 가능한 이벤트 하이웨이는 MSK. 같은 시스템 안에서 두세 가지를 동시에 쓰는 경우도 많다.

---

## 핵심 요약

- **SQS는 가장 단순한 비동기 큐**, Amazon MQ는 기존 브로커 이전 전문, MSK는 대용량 이벤트 스트리밍 전문
- **Redis는 캐시·세션에 가장 빠르지만** 진정한 메시지 큐로는 부족 — Pub/Sub은 메시지 손실 위험
- **복잡한 라우팅은 Amazon MQ, 순서 보장이 필수면 SQS FIFO, 여러 소비자 재사용은 MSK**

## 참고

- 원문: [Redis vs (Amazon MQ vs SQS vs MSK)](https://memoryhub.tistory.com/entry/SQS-Amazon-MQ-MSK-Redis-%EC%B0%A8%EC%9D%B4)
