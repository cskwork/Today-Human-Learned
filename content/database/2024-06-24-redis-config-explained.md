+++
title = "Redis 연결 설정 — Single, Sentinel, Cluster 세 가지 모드"
date = "2024-06-24"
description = "Spring 애플리케이션에서 Redis 연결 방식은 단일 노드, Sentinel, Cluster 세 가지이며, 운영 환경 설정값에 따라 코드 변경 없이 모드를 전환할 수 있도록 구성하는 것이 핵심이다."
tags = ["database"]
categories = ["database"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Spring 애플리케이션에서 Redis 연결 방식은 단일 노드, Sentinel, Cluster 세 가지이며, 운영 환경 설정값에 따라 코드 변경 없이 모드를 전환할 수 있도록 구성하는 것이 핵심이다.

---

## Redis 연결 설정을 왜 이해해야 하는지 감 잡기

Redis를 처음 도입하면 보통 localhost 한 대로 시작한다. 그런데 서비스가 성장하면 "Redis가 죽으면 어떡하지?" 또는 "데이터가 너무 많아 한 대에 안 들어가" 같은 문제가 생긴다. 이를 해결하기 위해 Sentinel(고가용성)과 Cluster(수평 확장)가 존재한다. Spring에서 이 세 가지를 코드 변경 없이 전환하려면, 연결 설정을 설정 파일에서 읽어오도록 구성해야 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 설정값 로드 → 모드 판별(single/sentinel/cluster) → LettuceConnectionFactory 생성 → RedisTemplate 주입`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| LettuceConnectionFactory | Spring이 Redis 서버와 실제로 연결하는 데 쓰는 객체. Lettuce는 Redis 클라이언트 라이브러리다. |
| RedisTemplate | Redis 명령어(GET, SET 등)를 Java 코드로 실행할 수 있게 감싸주는 객체. |
| Sentinel | Redis 마스터 장애 시 자동으로 레플리카를 마스터로 승격시키는 감시 프로세스. |
| Cluster | 데이터를 여러 Redis 노드에 나누어 저장하는 수평 확장 방식. |
| Serializer | Java 객체를 Redis가 저장할 수 있는 바이트로 변환하는 도구. 보통 StringRedisSerializer를 쓴다. |

## 예를 들어 설명하면

설정값으로 모드를 분기하는 핵심 로직은 아래와 같다.

```java
// redisMode 값에 따라 다른 ConnectionFactory를 반환한다
public LettuceConnectionFactory redisConnectionFactory() {
    RedisProperties props = fetchRedisProperties();
    return switch (props.redisMode) {
        case "sentinel" -> createSentinelConnectionFactory(props);
        case "cluster"  -> createClusterConnectionFactory(props);
        default         -> createSingleNodeConnectionFactory(props);
    };
}
```

각 모드의 특징 요약:

| 모드 | 핵심 목적 | 단점 |
|---|---|---|
| Single | 개발/소규모 환경 | 장애 시 서비스 중단 |
| Sentinel | 자동 장애 복구 | 수평 확장 불가 |
| Cluster | 대용량 + 고가용성 | 설정 복잡, 멀티키 제약 |

## 이 단계에서 중요한 판단 기준

운영 환경에서는 Single 모드를 쓰지 않는다 — Redis가 단일 장애점이 되면 전체 서비스가 멈추기 때문에 Sentinel 이상을 기본으로 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Redis 연결 설정은 설정값으로 모드를 분기해 LettuceConnectionFactory를 동적으로 생성하며, 운영 환경에는 Sentinel 또는 Cluster를 쓴다.**

## 나중에 더 깊게 들어가면

- Sentinel 구성에서 마스터 이름(master name)과 쿼럼(quorum) 설정
- Cluster 모드에서 멀티키 연산의 해시 슬롯 제약 처리
- TLS/SSL 적용 시 LettuceClientConfiguration 추가 설정

---

**원본:** [Redis Config Explained — https://memoryhub.tistory.com/314](https://memoryhub.tistory.com/314)
