# Spring Security — 웹 애플리케이션 보안의 기초

> **TL;DR**
> Spring Security는 인증(누구인지 확인)과 인가(무엇을 허용할지 결정)를 자동으로 처리해 주는 Spring 생태계의 보안 프레임워크다.

---

## Spring Security를 왜 쓰는지 감 잡기

웹 애플리케이션을 만들면 반드시 두 가지 문제가 생긴다. 첫째, 사용자가 자신이 주장하는 사람이 맞는지 확인해야 한다. 둘째, 확인된 사용자라도 모든 기능을 열어줘선 안 된다. 이 두 문제를 처음부터 직접 구현하면 보안 취약점이 생기기 쉽다. Spring Security는 이 공통 문제를 검증된 방식으로 대신 처리해 준다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 요청 수신 → 필터 체인 통과 → 인증 확인 → 인가 판단 → 리소스 접근`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 인증 (Authentication) | 로그인처럼, "이 사람이 정말 그 사람이 맞나"를 확인하는 과정 |
| 인가 (Authorization) | 인증된 사람이 특정 페이지나 기능에 들어갈 권한이 있는지 판단하는 과정 |
| CSRF | 다른 사이트에서 로그인된 사용자인 척 요청을 보내는 공격 방식 |
| 필터 체인 (Security Filter Chain) | 요청이 실제 컨트롤러에 닿기 전에 순서대로 통과하는 보안 검사 단계들 |
| UserDetailsService | 사용자 이름을 받아 DB에서 해당 사용자 정보(비밀번호, 역할)를 꺼내오는 인터페이스 |

## 예를 들어 설명하면

공개 페이지, 로그인 후 접근 페이지, 관리자 전용 페이지가 있는 앱을 예로 든다.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
                .antMatchers("/public/**").permitAll()
                .antMatchers("/private/**").authenticated()
                .antMatchers("/admin/**").hasRole("ADMIN")
            .and()
            .formLogin()
                .loginPage("/login").permitAll()
            .and()
            .logout().permitAll();
    }
}
```

`/public/**`은 누구나 접근 가능, `/private/**`는 로그인한 사람만, `/admin/**`은 ADMIN 역할을 가진 사람만 접근할 수 있다. URL 하나당 규칙 한 줄이다.

## 이 단계에서 중요한 판단 기준

URL별 접근 규칙을 가장 구체적인 경로부터 먼저 선언해야 한다. 순서가 바뀌면 더 넓은 규칙이 먼저 적용되어 의도하지 않게 접근이 허용된다.

## 한 줄 요약 — 이것만 기억하면 된다

**Spring Security는 필터 체인을 통해 모든 요청의 인증과 인가를 자동으로 처리하며, 설정 코드 몇 줄로 URL별 접근 규칙을 선언할 수 있다.**

## 나중에 더 깊게 들어가면

- JWT 기반 Stateless 인증 구성 방법
- OAuth2 / OpenID Connect 연동
- 메서드 수준 보안 (`@PreAuthorize`, `@Secured`)

---

**원본:** [Spring Security Introduced — https://memoryhub.tistory.com/108](https://memoryhub.tistory.com/108)
