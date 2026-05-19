# Spring Boot + JWT + Spring Security — 안전한 인증과 파일 업로드

> **TL;DR**
> JWT는 서버가 세션을 저장하지 않고도 사용자를 인증할 수 있는 토큰 방식이며, Spring Security 필터 체인에 끼워 넣으면 모든 요청을 자동으로 검문할 수 있다.

---

## 이 주제를 왜 쓰는지 감 잡기

로그인한 사용자를 매 요청마다 어떻게 알아볼까? 전통적인 방법은 서버가 세션을 메모리에 보관하는 것이다. 하지만 서버가 여러 대로 늘어나면 세션 공유 문제가 생긴다. JWT(JSON Web Token)는 인증 정보를 토큰 안에 담아 클라이언트가 들고 다니게 한다. 서버는 토큰의 서명만 검증하면 되므로 세션 저장소가 필요 없다.

Spring Security는 HTTP 요청이 컨트롤러에 도달하기 전에 통과해야 하는 필터 체인을 제공한다. 여기에 JWT 검증 필터를 하나 추가하면, 유효한 토큰 없이는 보호된 API에 접근할 수 없다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 로그인 요청 → AuthenticationManager 인증 → JWT 발급 → 이후 요청마다 필터가 토큰 검증 → SecurityContext 저장 → 컨트롤러 실행`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| JWT | 사용자 정보와 만료 시간을 Base64로 인코딩하고 서명을 붙인 문자열 토큰 |
| SecurityFilterChain | Spring Security가 요청을 처리하는 순서 있는 필터 목록 |
| UserDetails | Spring Security가 사용자를 표현하는 인터페이스 — 이름, 비밀번호, 권한을 담는다 |
| AuthenticationManager | 아이디/비밀번호를 받아 실제 인증을 수행하는 관리자 역할 |
| SecurityContext | 현재 요청 스레드에서 "누가 로그인했는지"를 저장해 두는 공간 |

## 예를 들어 설명하면

로그인 후 파일을 업로드하는 시나리오다.

```
# 1. 로그인 — JWT 발급
POST /api/auth/login
Body: { "username": "alice", "password": "secret" }

Response Header: Authorization: Bearer eyJhbGciOiJIUzUxMiJ9...

# 2. 파일 업로드 — JWT 첨부
POST /api/files/upload
Header: Authorization: Bearer eyJhbGciOiJIUzUxMiJ9...
Body: multipart/form-data { file: report.pdf }
```

JWT 필터(`JwtAuthenticationFilter`)는 모든 요청에서 `Authorization` 헤더를 읽어 `Bearer ` 접두사를 제거한 뒤, `JwtTokenProvider.validateToken()`으로 서명과 만료 시간을 확인한다. 통과하면 `SecurityContextHolder`에 인증 객체를 저장하고, 실패하면 아무것도 저장하지 않는다. 이후 Security 설정이 미인증 요청을 차단한다.

파일 저장 시에는 원본 파일명에 UUID를 붙여 저장한다. 경로 조작 공격을 막으려면 `Paths.get(originalFileName).getFileName().toString()`으로 경로 부분을 제거해야 한다.

## 이 단계에서 중요한 판단 기준

JWT 비밀 키(`secret-key`)는 절대 코드나 저장소에 하드코딩하지 말고, 환경 변수나 시크릿 관리 서비스에서 주입받아야 한다 — 유출 시 누구나 유효한 토큰을 위조할 수 있다.

## 한 줄 요약 — 이것만 기억하면 된다

**JWT 필터를 Spring Security 체인에 등록하면 서버 세션 없이도 모든 요청에서 인증 상태를 유지할 수 있다.**

## 나중에 더 깊게 들어가면

- Refresh Token 도입 — Access Token 만료 후 재발급 흐름
- `@PreAuthorize`, `@PostAuthorize`로 메서드 수준 권한 제어
- 파일 업로드 보안 강화 — Content-Type 검증, 악성코드 스캔

---

**원본:** [Spring Boot + JWT + Spring Security - 안전한 인증과 파일 업로드 완전 정복](https://memoryhub.tistory.com/544)
