+++
title = "Redis — 메모리에 올려두는 초고속 데이터 저장소"
date = "2024-06-02"
description = "Redis는 데이터를 디스크가 아닌 RAM에 올려두기 때문에 일반 데이터베이스보다 수십 배 빠르다. 캐시, 순위표, 메시지 큐 등 속도가 중요한 곳에 쓴다."
tags = ["database"]
categories = ["database"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Redis는 데이터를 디스크가 아닌 RAM에 올려두기 때문에 일반 데이터베이스보다 수십 배 빠르다. 캐시, 순위표, 메시지 큐 등 속도가 중요한 곳에 쓴다.

---

## Redis를 왜 쓰는지 감 잡기

웹 서비스에서 "유저 정보를 DB에서 매번 조회하면 느리다"는 문제가 생긴다. 디스크에 저장된 관계형 DB는 요청마다 파일 I/O가 발생한다. Redis는 데이터를 메모리(RAM)에 올려두므로 조회가 마이크로초 단위로 끝난다.

원래 이름은 Remote Dictionary Server다. 단순한 키-값 저장소에서 시작했지만, 지금은 리스트, 집합, 정렬된 집합, 해시 등 여러 자료구조를 지원한다. 그래서 캐시뿐 아니라 실시간 순위표, 세션 저장, 메시지 브로커로도 쓴다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 요청 → Redis(메모리 조회) → 없으면 DB 조회 → Redis에 저장 → 응답`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 인메모리(In-memory) | 하드디스크 대신 RAM에 데이터를 올려두는 방식. 빠르지만 서버가 꺼지면 사라질 수 있다. |
| Sorted Set | 각 항목에 점수(score)를 매겨 순서를 유지하는 집합. 게임 순위표에 딱 맞는 구조다. |
| TTL (Time To Live) | 데이터가 자동으로 사라지기까지의 시간. 캐시가 오래된 값을 유지하지 않도록 설정한다. |
| Pub/Sub | 발행(Publish)과 구독(Subscribe). 메시지를 보내면 구독 중인 클라이언트가 즉시 받는다. |
| 영속성(Persistence) | 메모리 데이터를 디스크에 저장하는 기능. RDB(스냅샷)와 AOF(명령 로그) 두 방식이 있다. |

## 예를 들어 설명하면

게임 서버에서 실시간 순위표를 만들어야 한다. 플레이어 수천 명의 점수가 매 초 바뀐다면 RDB로는 감당이 어렵다. Redis의 Sorted Set을 쓰면 한 줄로 해결된다.

```
# 점수 추가/갱신
ZADD leaderboard 100 "player1"
ZADD leaderboard 95 "player3"

# 상위 3명 조회 (점수 내림차순)
ZREVRANGE leaderboard 0 2 WITHSCORES

# player1의 점수 15점 추가
ZINCRBY leaderboard 15 "player1"
```

조회 결과는 항상 점수 순으로 정렬되어 있고, 변경도 O(log N)이라 빠르다.

## 이 단계에서 중요한 판단 기준

"데이터가 자주 읽히고, 잠깐 틀려도 괜찮은가?" — 그렇다면 Redis 캐시가 답이다. 금융 트랜잭션처럼 정확성이 최우선이라면 Redis만으로는 부족하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Redis는 자주 읽는 데이터를 RAM에 올려두어 응답 속도를 극적으로 줄이는 인메모리 데이터 저장소다.**

## 나중에 더 깊게 들어가면

- Redis Sentinel과 Cluster로 고가용성 구성하기
- RDB와 AOF 영속성 옵션의 트레이드오프
- Redis를 이용한 분산 락(Distributed Lock) 구현

---

**원본:** [Redis Introduced — https://memoryhub.tistory.com/178](https://memoryhub.tistory.com/178)
