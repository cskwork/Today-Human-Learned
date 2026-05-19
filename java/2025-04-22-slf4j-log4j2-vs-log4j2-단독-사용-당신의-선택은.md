# SLF4J + Log4j2 vs Log4j2 단독 사용 — 무엇을 선택해야 하나

> **TL;DR**
> 코드가 로깅 구현체에 직접 묶이지 않으려면 SLF4J를 앞에 두고 Log4j2를 뒤에서 실행하는 것이 표준이다.

---

## 이 주제를 왜 쓰는지 감 잡기

Log4j2는 Java 진영에서 가장 성능이 좋은 로깅 프레임워크 중 하나다. 그런데 Log4j2를 코드에서 직접 임포트해서 쓰면, 나중에 다른 로깅 라이브러리로 교체할 때 소스 파일 전체를 수정해야 한다.

SLF4J(Simple Logging Facade for Java)는 로깅 추상화 계층이다. 코드는 SLF4J의 Logger 인터페이스만 바라보고, 실제 로그를 처리하는 엔진은 설정 파일과 바인딩 라이브러리로 갈아 끼울 수 있다. 마치 JDBC 인터페이스 뒤에서 실제 드라이버가 교체되는 것과 같은 구조다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 코드(SLF4J API) → 바인딩 라이브러리 → Log4j2 Core → 파일/콘솔 출력`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| SLF4J | 로깅 추상화 인터페이스 — 어떤 구현체를 쓰든 코드는 같은 API를 호출한다 |
| Log4j2 | 실제 로그를 처리하는 엔진 — 비동기 처리와 다양한 출력 형식을 지원한다 |
| 바인딩(Binding) | SLF4J 호출을 특정 로깅 구현체로 연결해 주는 어댑터 라이브러리 |
| 결합도(Coupling) | 코드가 특정 라이브러리에 얼마나 강하게 묶여 있는지의 정도 |
| 로깅 파사드(Facade) | 실제 구현을 감추고 통일된 인터페이스를 제공하는 설계 패턴 |

## 예를 들어 설명하면

두 방식의 의존성과 코드 차이를 비교하면 다음과 같다.

```java
// SLF4J + Log4j2 방식 — 구현체 교체 시 이 코드는 변경 불필요
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

private static final Logger log = LoggerFactory.getLogger(MyService.class);
log.info("처리 완료: {}", result);

// Log4j2 단독 방식 — 구현체 교체 시 이 임포트를 전부 바꿔야 함
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

private static final Logger log = LogManager.getLogger(MyService.class);
log.info("처리 완료: {}", result);
```

Gradle 의존성 기준으로, SLF4J + Log4j2 방식은 `slf4j-api`, `log4j-slf4j2-impl`, `log4j-core`가 필요하다. Log4j2 단독은 `log4j-api`와 `log4j-core`만 있으면 된다.

## 이 단계에서 중요한 판단 기준

외부에 배포되는 라이브러리를 만들거나, 팀 프로젝트에서 장기 유지보수가 필요한 코드라면 SLF4J를 써야 한다 — 사용자가 어떤 로깅 구현체를 선택하든 충돌이 없어야 하기 때문이다.

| 구분 | SLF4J + Log4j2 | Log4j2 단독 |
|---|---|---|
| 구현체 교체 | 바인딩만 교체, 코드 수정 없음 | 코드 전체 수정 필요 |
| 외부 라이브러리 호환 | SLF4J 사용 라이브러리와 자동 통합 | 브릿지 라이브러리 추가 설정 필요 |
| 의존성 수 | 3개 이상 | 2개 |
| 추천 상황 | 대부분의 프로젝트, 라이브러리 개발 | 소규모 독립 앱, 교체 가능성 없음 |

## 한 줄 요약 — 이것만 기억하면 된다

**대부분의 프로젝트에서는 SLF4J + Log4j2를 쓴다 — 코드는 인터페이스에, 구현은 설정에 맡기는 것이 나중에 덜 고통스럽다.**

## 나중에 더 깊게 들어가면

- Log4j2 비동기 로거(AsyncLogger) 설정과 성능 튜닝
- Logback vs Log4j2 — SLF4J 바인딩 구현체 비교
- MDC(Mapped Diagnostic Context)로 요청 ID를 모든 로그 줄에 자동 첨부하는 방법

---

**원본:** [SLF4J + Log4j2 vs Log4j2 단독 사용 - 당신의 선택은](https://memoryhub.tistory.com/556)
