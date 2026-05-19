+++
title = "Ehcache — Java 애플리케이션을 위한 로컬 캐시"
date = "2024-06-02"
description = "Ehcache는 Java 앱에서 DB나 외부 API 호출 횟수를 줄이기 위해 결과를 메모리에 잠시 저장해 두는 캐시 라이브러리다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Ehcache는 Java 앱에서 DB나 외부 API 호출 횟수를 줄이기 위해 결과를 메모리에 잠시 저장해 두는 캐시 라이브러리다.

---

## Ehcache를 왜 쓰는지 감 잡기

웹 서비스에서 같은 데이터를 수백 번 DB에 요청하면 응답이 느려지고 DB 부하가 커진다. 캐시는 한 번 조회한 데이터를 빠른 저장소(메모리)에 복사해 두고, 동일한 요청이 오면 DB 대신 캐시에서 즉시 돌려주는 방식이다. Ehcache는 Java 표준 캐시 API(JSR-107)를 구현한 라이브러리로, 설정 파일 하나로 메모리·오프힙·디스크를 계층적으로 쓸 수 있다. Spring Boot와 조합하면 어노테이션 몇 개만으로 메서드 결과를 자동으로 캐싱할 수 있다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 요청 도착 → 캐시 확인(히트/미스) → 히트면 즉시 반환, 미스면 DB 조회 후 캐시에 저장 → 반환`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 캐시 히트 | 요청한 데이터가 이미 캐시에 있어서 빠르게 반환된 경우 |
| 캐시 미스 | 캐시에 데이터가 없어서 원본 저장소(DB 등)에서 가져와야 하는 경우 |
| 퇴거 정책 | 캐시가 꽉 찼을 때 어떤 데이터를 먼저 지울지 결정하는 규칙 (LRU, LFU, FIFO 등) |
| 오프힙 | Java 힙 외부 메모리에 저장하는 방식으로, GC 멈춤 없이 대용량 캐시를 유지할 수 있다 |
| TTL | Time To Live. 캐시 항목이 자동으로 만료되기까지의 유효 시간 |

## 예를 들어 설명하면

Maven 프로젝트에 의존성을 추가하고, Java 코드에서 캐시를 생성·사용한다.

```java
CacheManager cacheManager = CacheManagerBuilder.newCacheManagerBuilder()
    .withCache("userCache",
        CacheConfigurationBuilder.newCacheConfigurationBuilder(
            Long.class, String.class,
            ResourcePoolsBuilder.heap(100)))
    .build();
cacheManager.init();

Cache<Long, String> cache = cacheManager.getCache("userCache", Long.class, String.class);

cache.put(1L, "Alice");          // 캐시에 저장
String name = cache.get(1L);    // "Alice" — DB 없이 즉시 반환
cacheManager.close();
```

처음 `get(1L)` 호출 시 캐시에 없으면 미스가 발생해 DB를 조회하고, 이후 동일한 키로 요청이 오면 히트가 발생해 메모리에서 바로 반환한다.

## 이 단계에서 중요한 판단 기준

캐시는 자주 읽히고 변경이 드문 데이터에만 적용한다. 데이터가 자주 바뀌는데 TTL 없이 캐싱하면 오래된 값이 반환되는 오염 문제가 생긴다.

## 한 줄 요약 — 이것만 기억하면 된다

**Ehcache는 "같은 DB 조회를 반복하지 않도록" 메모리에 결과를 잠시 보관하는 Java 캐시 라이브러리다.**

## 나중에 더 깊게 들어가면

- Spring의 `@Cacheable`, `@CacheEvict` 어노테이션으로 선언적 캐싱 적용하기
- Redis 같은 분산 캐시와 Ehcache(로컬 캐시)의 차이 및 선택 기준
- 캐시 스탬피드(Thundering Herd) 문제와 방어 전략

---

**원본:** [Ehcache Introduced — https://memoryhub.tistory.com/177](https://memoryhub.tistory.com/177)
