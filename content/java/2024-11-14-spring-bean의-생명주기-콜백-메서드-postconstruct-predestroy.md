+++
title = "Spring Bean 생명주기 콜백 — @PostConstruct & @PreDestroy"
date = "2024-11-14"
description = "Bean이 준비되자마자 실행할 초기화 코드는 `@PostConstruct`, 컨테이너가 꺼지기 직전 정리 코드는 `@PreDestroy`에 넣으면 된다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Bean이 준비되자마자 실행할 초기화 코드는 `@PostConstruct`, 컨테이너가 꺼지기 직전 정리 코드는 `@PreDestroy`에 넣으면 된다.

---

## @PostConstruct / @PreDestroy를 왜 쓰는지 감 잡기

스프링이 Bean을 만들 때는 세 단계를 거친다. 생성자 호출 → 의존성 주입 → 실제 사용. 문제는 생성자 안에서는 아직 의존 객체가 주입되지 않은 상태라는 점이다. DB 커넥션 풀을 미리 채우거나, 캐시를 워밍업하거나, 설정 파일을 읽어야 한다면 의존성 주입이 끝난 뒤 실행해야 안전하다.

반대로 애플리케이션이 종료될 때 열어둔 소켓이나 DB 연결을 그냥 놔두면 리소스가 누수된다. 이 두 시점을 정확히 잡아주는 어노테이션이 `@PostConstruct`와 `@PreDestroy`다.

`핵심 흐름: 생성자 → @PostConstruct → (서비스 중) → @PreDestroy → 소멸`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Bean 생명주기 | 스프링이 객체를 만들고 쓰다가 버리기까지의 전 과정 |
| @PostConstruct | 의존성 주입이 완료된 직후 한 번 실행되는 초기화 메서드 표시 |
| @PreDestroy | 스프링 컨테이너가 Bean을 제거하기 직전에 실행되는 정리 메서드 표시 |
| 의존성 주입 | 스프링이 필요한 객체를 자동으로 연결하는 것 |
| 스프링 컨테이너 | Bean들을 만들고 관리하는 스프링의 핵심 공간 |

## 예를 들어 설명하면

파일을 자주 읽는 서비스가 있다고 하자. S3에서 매번 가져오면 느리니까 시작할 때 캐시를 채우고, 종료할 때 임시 파일을 지운다.

```java
@Service
public class FileService {

    private Map<String, byte[]> fileCache;

    @PostConstruct
    public void initializeCache() {
        fileCache = new HashMap<>();
        downloadFrequentlyUsedFiles(); // 시작 시 캐시 워밍업
    }

    @PreDestroy
    public void clearCache() {
        fileCache.clear();
        deleteTempFiles(); // 종료 시 임시 파일 정리
    }
}
```

`@PostConstruct`가 붙은 메서드는 반환 타입이 `void`이고 파라미터가 없어야 한다.

## 이 단계에서 중요한 판단 기준

생성자 안에서 무언가를 초기화하려 할 때 NullPointerException이 나온다면, 의존 객체가 아직 주입되지 않은 것이므로 그 로직을 `@PostConstruct` 메서드로 옮겨야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**준비는 `@PostConstruct`, 정리는 `@PreDestroy` — Bean 생명주기의 시작과 끝을 이 두 어노테이션이 맡는다.**

## 나중에 더 깊게 들어가면

- `InitializingBean` / `DisposableBean` 인터페이스 방식과의 차이
- `@Bean(initMethod = "...", destroyMethod = "...")`으로 XML 없이 같은 효과 내기
- 강제 종료(kill -9)에도 정리 코드를 실행하려면 ShutdownHook 직접 등록하는 법

---

**원본:** [Spring Bean의 생명주기 콜백 메서드 (@PostConstruct & @PreDestroy)](https://memoryhub.tistory.com/372)
