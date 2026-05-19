# OAuth — 내 비밀번호를 넘기지 않고 권한을 위임하는 방법

> **TL;DR**
> OAuth는 사용자가 직접 자격증명을 공유하지 않고도 제3자 애플리케이션에 특정 권한만 위임할 수 있게 하는 표준 프로토콜이다.

---

## OAuth를 왜 쓰는지 감 잡기

어떤 서비스에서 "Google로 로그인" 버튼을 누르면 Google 로그인 창이 뜨고, 허락하면 해당 서비스가 내 구글 프로필을 읽는다. 이때 내 구글 비밀번호를 그 서비스에 알려준 적은 없다. OAuth가 이 마법을 가능하게 한다.

집 열쇠를 통째로 빌려주는 대신, 특정 방만 열 수 있는 임시 카드키를 발급하는 구조다. 카드키(액세스 토큰)는 만료되고, 권한 범위도 제한된다.

`핵심 흐름: 사용자 허락 요청 → 인증 서버 승인 → 액세스 토큰 발급 → 토큰으로 리소스 접근`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Resource Owner | 권한을 위임하는 사용자 본인. |
| Client | 권한을 요청하는 애플리케이션. 예: "Google로 로그인"을 제공하는 서비스. |
| Authorization Server | 사용자를 인증하고 토큰을 발급하는 서버. 예: Google의 OAuth 서버. |
| Resource Server | 보호된 데이터를 갖고 있는 서버. 예: Google 프로필 API. |
| Access Token | 클라이언트가 리소스 서버에 제출하는 임시 열쇠. 만료 시간이 있다. |

## 예를 들어 설명하면

Spring Boot에서 Google OAuth2 로그인을 붙이는 최소 구성이다.

```yaml
# application.yml
spring:
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: YOUR_CLIENT_ID
            client-secret: YOUR_CLIENT_SECRET
            scope: profile, email
```

```java
// Security 설정 — OAuth2 로그인 활성화
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/").permitAll()
                .anyRequest().authenticated()
            .and()
            .oauth2Login();
    }
}
```

Spring Security가 나머지 흐름(리디렉션, 코드 교환, 토큰 저장)을 자동으로 처리한다. 로그인 성공 후 `@AuthenticationPrincipal OAuth2User`로 사용자 정보를 받을 수 있다.

## 이 단계에서 중요한 판단 기준

`client-id`와 `client-secret`은 절대 코드에 하드코딩하지 않는다. 환경 변수나 시크릿 매니저를 통해 주입해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**OAuth는 비밀번호 대신 한시적 토큰으로 권한을 위임한다. 사용자는 어떤 권한을 줄지 직접 선택한다.**

## 나중에 더 깊게 들어가면

- Authorization Code Flow vs Implicit Flow vs Client Credentials Flow 비교
- Refresh Token으로 액세스 토큰 갱신하는 방법
- OAuth 2.0과 OpenID Connect(OIDC)의 차이

---

**원본:** [OAuth Introduced — https://memoryhub.tistory.com/300](https://memoryhub.tistory.com/300)
