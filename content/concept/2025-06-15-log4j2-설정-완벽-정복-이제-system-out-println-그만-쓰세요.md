+++
title = "Log4j2 — System.out.println 대신 제대로 된 로깅을 쓰는 이유"
date = "2025-06-15"
description = "Log4j2는 Java 애플리케이션의 로그를 파일로 관리하고 비동기로 처리하는 프레임워크로, System.out.println과 달리 레벨 제어·파일 롤링·성능 최적화를 제공한다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Log4j2는 Java 애플리케이션의 로그를 파일로 관리하고 비동기로 처리하는 프레임워크로, System.out.println과 달리 레벨 제어·파일 롤링·성능 최적화를 제공한다.

---

## Log4j2를 왜 쓰는지 감 잡기

운영 서버에서 System.out.println으로 찍은 로그는 콘솔에만 출력되고 파일에 남지 않는다. 장애가 났을 때 이미 출력은 사라지고 없다. 또 로그가 너무 많으면 텍스트 연산 자체가 CPU를 잡아먹는다.

Log4j2는 이 문제를 세 가지로 해결한다. 첫째, 로그를 파일에 저장하고 크기나 날짜 기준으로 자동 압축·삭제한다(롤링). 둘째, 레벨(DEBUG/INFO/WARN/ERROR)로 운영 환경에서는 불필요한 로그를 필터링한다. 셋째, 비동기 처리로 로그 기록이 주 처리 흐름을 막지 않게 한다.

`핵심 흐름: 코드에서 logger.info() 호출 → Appender가 출력 대상 결정 → Layout이 형식 지정 → 파일/콘솔에 기록`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Logger | 로그를 찍는 주체 — 보통 클래스마다 하나씩 생성 |
| Appender | 로그를 어디에 출력할지 정하는 설정 (콘솔, 파일, 원격 서버 등) |
| RollingFile | 파일이 특정 크기나 날짜를 넘으면 자동으로 새 파일로 교체하는 Appender |
| MDC (Mapped Diagnostic Context) | 요청 ID나 사용자 ID처럼 스레드에 붙여두는 키-값 정보 — 모든 로그 줄에 자동 포함됨 |
| 비동기 로깅 | 로그 기록을 별도 스레드에서 처리해 주 요청 처리 속도에 영향을 주지 않는 방식 |

## 예를 들어 설명하면

Spring Boot 프로젝트에 Log4j2를 적용하려면 기본 포함된 Logback을 먼저 제외하고, Log4j2 의존성을 추가한다.

```groovy
// build.gradle
configurations {
    all {
        exclude group: 'org.springframework.boot', module: 'spring-boot-starter-logging'
    }
}
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-log4j2'
    implementation 'com.lmax:disruptor:3.4.4'  // 비동기 로깅용
}
```

`src/main/resources/log4j2.xml`에서 RollingFile Appender를 설정하면, 로그 파일이 100MB를 넘거나 자정이 되면 자동으로 날짜별 gzip 파일로 교체된다. 30일이 지난 파일은 자동 삭제된다.

코드에서는 아래처럼 사용한다.

```java
private static final Logger logger = LogManager.getLogger(UserService.class);

// 람다를 쓰면 DEBUG 레벨이 꺼져 있을 때 문자열 연산 자체를 건너뜀
logger.debug("검증 시작 - 이메일: {}", () -> userDto.getEmail());
```

## 이 단계에서 중요한 판단 기준

Spring Boot 기본값(Logback)에서 Log4j2로 교체할 가치가 있는 시점은 비동기 로깅이나 람다 표현식이 필요할 때다 — 단순 파일 로깅만 필요하면 Logback으로도 충분하다.

## 한 줄 요약 — 이것만 기억하면 된다

**Log4j2는 레벨 필터링·파일 롤링·비동기 처리를 제공해 System.out.println보다 운영 환경에 적합하며, Spring Boot에서는 Logback 의존성을 제외하고 log4j2.xml을 추가하는 것만으로 전환할 수 있다.**

## 나중에 더 깊게 들어가면

- MDC와 분산 추적(Trace ID)으로 마이크로서비스 환경에서 요청 흐름 추적하기
- JSON 형식 로그로 Elasticsearch/Grafana 같은 로그 분석 도구와 연동하기
- Log4j2 성능 벤치마크와 Disruptor 기반 AsyncLogger 설정

---

**원본:** Log4j2 설정 완벽 정복 - 이제 System.out.println() 그만 쓰세요! — https://memoryhub.tistory.com/688
