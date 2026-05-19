# Spring Boot WireMock 테스팅 — 외부 API 의존성 제거하는 진짜 방법

> **TL;DR**
> WireMock은 테스트 실행 시간에만 뜨는 가짜 HTTP 서버다. 외부 API가 죽어도, 느려도, 돈이 들어도 테스트는 항상 빠르고 안정적으로 돌아간다.

---

## WireMock을 왜 쓰는지 감 잡기

외부 결제 API나 배송 조회 API를 호출하는 서비스를 테스트할 때 실제 API를 그대로 쓰면 세 가지 문제가 생긴다. 외부 서버가 다운되면 테스트도 실패하고, 응답이 느리면 CI 빌드 시간이 길어지고, API 호출 횟수에 따라 비용이 발생한다.

`@MockBean`(Mockito)으로 HTTP 클라이언트 자체를 모킹하면 빠르긴 한데, 타임아웃 처리나 404 응답 시 동작처럼 실제 HTTP 계층에서 발생하는 로직을 검증할 수 없다.

WireMock은 진짜 HTTP 서버를 테스트 프로세스 안에서 띄운다. 애플리케이션은 실제 HTTP 요청을 보내고, WireMock이 미리 정의한 응답(Stub)을 돌려준다.

`핵심 흐름: 의존성 추가 → @EnableWireMock 선언 → stubFor()로 응답 정의 → 테스트 실행 → verify()로 요청 검증`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Stub | "이 URL로 오면 이 응답을 줘라"고 미리 정의한 규칙 |
| @EnableWireMock | WireMock 서버를 자동으로 시작·종료해주는 Spring Boot 어노테이션 |
| @ConfigureWireMock | 이름과 포트를 지정해 독립된 WireMock 인스턴스를 만들 때 씀 |
| @InjectWireMock | 특정 이름의 WireMock 인스턴스를 테스트 클래스 필드에 주입하는 어노테이션 |
| wiremock.server.baseUrl | Spring Context에 자동 등록되는 프로퍼티. 동적으로 할당된 포트 URL을 테스트에서 꺼내 쓸 때 사용 |

## 예를 들어 설명하면

외부 사용자 API를 호출하는 서비스를 테스트하는 기본 구조다.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@EnableWireMock
class UserClientTest {

    @Value("${wiremock.server.baseUrl}")
    private String wireMockBaseUrl;

    @Test
    void 외부_API_타임아웃_시_예외가_발생해야_한다() {
        // 5초 지연 — FeignClient의 Read Timeout 동작 검증
        stubFor(get("/api/users/1")
            .willReturn(aResponse().withFixedDelay(5000).withStatus(200)));

        assertThrows(ResourceAccessException.class, () ->
            restTemplate.getForEntity(wireMockBaseUrl + "/api/users/1", String.class));
    }

    @Test
    void 정상_응답이면_사용자_객체를_반환해야_한다() {
        stubFor(get("/api/users/1")
            .willReturn(okJson("{\"id\":1,\"name\":\"Alice\"}")));

        ResponseEntity<String> res =
            restTemplate.getForEntity(wireMockBaseUrl + "/api/users/1", String.class);

        assertEquals(200, res.getStatusCode().value());
        verify(exactly(1), getRequestedFor(urlEqualTo("/api/users/1")));
    }
}
```

여러 외부 API를 동시에 테스트할 때는 `@ConfigureWireMock`으로 각각 이름을 붙인 인스턴스를 만들고 `@InjectWireMock`으로 필드에 주입해서 서로 독립적으로 Stub을 정의한다.

## 이 단계에서 중요한 판단 기준

Spring Boot 3.x에서는 반드시 `wiremock-spring-boot:3.9.0` 이상(또는 `wiremock-jetty12:3.10.0`)을 써야 한다. 구버전 WireMock은 `javax` 패키지 기반이라 Jakarta EE로 전환된 Spring Boot 3에서 `NoClassDefFoundError`가 난다.

## 한 줄 요약 — 이것만 기억하면 된다

**WireMock은 실제 외부 API 없이 HTTP 계층 전체를 검증할 수 있는 유일한 도구다 — Mockito가 커버 못 하는 타임아웃·에러 처리를 여기서 테스트하라.**

## 나중에 더 깊게 들어가면

- JSON 파일 기반 Stub 관리(`src/test/resources/mappings/*.json`)로 Stub 재사용성 높이기
- Record & Playback 모드로 실제 API 응답을 한 번 녹화해 Stub으로 재생하기
- Testcontainers WireMock으로 Docker 기반 격리 환경 구성하기

---

**원본:** [Spring Boot WireMock 테스팅, 외부 API 의존성 제거하는 진짜 방법](https://memoryhub.tistory.com/877)
