# Spring Boot Log4j2 최적화 설정 가이드

> **TL;DR**
> Log4j2를 비동기 모드로 바꾸고, 운영 환경에서 JSON 로그를 파일에 쓰고, MDC로 요청을 추적하면 로깅 문제의 90%가 해결된다.

---

## Log4j2를 왜 쓰는지 감 잡기

Spring Boot는 기본으로 Logback을 쓴다. 그런데 트래픽이 늘면 로깅 자체가 성능 병목이 된다. Log4j2는 LMAX Disruptor 라이브러리를 이용한 비동기 로거를 제공해서 동기 로깅보다 최대 100배 높은 처리량을 낸다. 또한 운영/개발 환경을 같은 XML 파일 안에서 `SpringProfile`로 분리할 수 있어 설정 관리가 편하다.

Log4j2를 쓰려면 먼저 Logback을 의존성에서 제거해야 한다. 두 라이브러리가 같이 있으면 충돌이 난다.

`핵심 흐름: 의존성 교체 → log4j2-spring.xml 작성 → 환경별 프로파일 분리 → 비동기 로거 활성화`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| AsyncLogger | 로그 메시지를 별도 스레드에서 처리해 애플리케이션 스레드를 블록하지 않는 로거 |
| Disruptor | Log4j2가 비동기 로깅에 사용하는 링 버퍼 기반 고성능 큐 라이브러리 |
| MDC (Mapped Diagnostic Context) | 스레드별로 key-value를 저장해 요청 ID 같은 컨텍스트를 로그에 자동으로 붙이는 기능 |
| RollingRandomAccessFile | 일반 RollingFile보다 I/O 성능이 좋은 어펜더. 운영 환경 파일 로깅에 권장 |
| JsonTemplateLayout | 로그를 JSON으로 출력하는 레이아웃. ELK/Datadog 같은 로그 수집 시스템과 연동할 때 사용 |

## 예를 들어 설명하면

운영 서버에서 사용자 요청마다 `requestId`를 추적하는 전형적인 패턴이다.

```java
// 요청마다 MDC에 ID를 심는 필터
@Component
public class RequestLoggingFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
            throws IOException, ServletException {
        MDC.put("requestId", UUID.randomUUID().toString());
        try {
            chain.doFilter(req, res);
        } finally {
            MDC.clear(); // 메모리 누수 방지 — 반드시 clear 해야 한다
        }
    }
}
```

로그 패턴에 `%X{requestId}`를 넣으면 모든 로그 줄에 자동으로 요청 ID가 붙는다. 비동기 환경(`@Async`)에서는 자식 스레드로 MDC가 자동 복사되지 않으므로 `TaskDecorator`를 따로 구현해야 한다.

비동기 로거 활성화는 JVM 옵션 한 줄이면 된다.

```
-DLog4jContextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
```

## 이 단계에서 중요한 판단 기준

운영 환경 패턴에서 `%L`(라인 번호), `%M`(메서드명), `%l`(위치 전체)을 쓰면 리플렉션 비용이 크게 오르므로 개발 환경에만 쓰고 운영에서는 반드시 제거하라.

## 한 줄 요약 — 이것만 기억하면 된다

**비동기 로거 + JSON 파일 어펜더 + MDC 요청 추적이 운영 Log4j2 설정의 세 기둥이다.**

## 나중에 더 깊게 들어가면

- ELK Stack(Elasticsearch, Logstash, Kibana)과 JsonTemplateLayout 연동 설정
- Kubernetes 사이드카 패턴으로 로그 중앙 수집하기
- GDPR 대응을 위한 PII 마스킹 커스텀 컨버터 구현

---

**원본:** [Spring Boot Log4j2 최적화 설정 가이드](https://memoryhub.tistory.com/701)
