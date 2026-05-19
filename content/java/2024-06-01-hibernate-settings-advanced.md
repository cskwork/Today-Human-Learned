+++
title = "Hibernate 고급 설정 이해하기"
date = "2024-06-01"
description = "Hibernate의 배치 처리, 2차 캐시, 통계 수집 설정을 `application.properties`에 추가하면 DB 왕복 횟수를 줄이고 성능 병목을 진단할 수 있다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Hibernate의 배치 처리, 2차 캐시, 통계 수집 설정을 `application.properties`에 추가하면 DB 왕복 횟수를 줄이고 성능 병목을 진단할 수 있다.

---

## Hibernate 고급 설정을 왜 알아야 하는지 감 잡기

기본 Hibernate 설정(DB URL, dialect, `ddl-auto`)만으로는 소규모 프로젝트에 충분하다. 그러나 데이터가 많아지면 두 가지 문제가 생긴다. 첫째, 루프 안에서 INSERT/UPDATE가 건당 DB 왕복을 만들어 속도가 크게 떨어진다. 둘째, 어디서 느려지는지 파악할 수단이 없다. 고급 설정은 이 두 문제를 다룬다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 엔티티 변경 감지 → 배치로 묶기 → DB 한 번에 전달 → 통계로 확인`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| batch_size | 한 번의 DB 전송에 묶을 INSERT/UPDATE 수. 클수록 왕복이 줄어든다 |
| 2차 캐시 | 세션을 넘어 공유되는 캐시. 자주 읽히는 참조 데이터에 적합 |
| default_batch_fetch_size | 연관 컬렉션을 IN 절로 묶어 로드하는 크기. N+1 완화에 직접 관련 |
| generate_statistics | 쿼리 횟수, 캐시 히트율 등을 수집. 성능 분석 시작점 |
| hbm2ddl.auto | 앱 시작 시 스키마를 어떻게 처리할지. 운영에서는 반드시 `validate` 또는 `none` |

## 예를 들어 설명하면

아래는 `application.properties`에서 자주 쓰는 고급 옵션을 묶은 예시다.

```properties
# 기본 연결
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=validate

# 배치 처리 — 루프 INSERT/UPDATE 성능 향상
spring.jpa.properties.hibernate.jdbc.batch_size=20
spring.jpa.properties.hibernate.order_inserts=true
spring.jpa.properties.hibernate.order_updates=true

# N+1 완화 — 컬렉션 로딩을 IN 절로 묶음
spring.jpa.properties.hibernate.default_batch_fetch_size=16

# 2차 캐시 (EhCache 예시)
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.ehcache.EhCacheRegionFactory
spring.jpa.properties.hibernate.cache.use_query_cache=true

# 로깅 & 통계 — 개발/스테이징 환경에서만 활성화
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.generate_statistics=true
```

`generate_statistics=true`를 켜면 로그에 쿼리 실행 횟수와 캐시 히트율이 찍힌다. 운영에서는 오버헤드가 있으므로 평소에는 끄고 분석이 필요할 때만 켠다.

## 이 단계에서 중요한 판단 기준

`hbm2ddl.auto=update`는 로컬 개발 편의를 위한 것이다 — 운영 DB에 적용하면 의도치 않은 스키마 변경이 발생할 수 있으므로, 운영에서는 반드시 `validate` 또는 `none`으로 설정하고 마이그레이션 도구(Flyway, Liquibase)를 사용한다.

## 한 줄 요약 — 이것만 기억하면 된다

**배치 사이즈로 왕복을 줄이고, 통계 수집으로 병목을 찾고, 캐시로 반복 읽기를 흡수한다.**

## 나중에 더 깊게 들어가면

- Flyway / Liquibase를 이용한 스키마 마이그레이션 자동화
- `@Cache` 어노테이션으로 엔티티별 2차 캐시 전략 설정
- Hibernate Statistics를 Micrometer / Grafana로 시각화하기

---

**원본:** [Hibernate Settings Advanced](https://memoryhub.tistory.com/168)
