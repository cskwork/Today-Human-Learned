+++
title = "Keycloak SSO를 JavaScript로 연동하기"
date = "2025-02-09"
description = "Keycloak JS Adapter 하나로 초기화하면 로그인/로그아웃/토큰 관리를 직접 구현하지 않아도 SSO가 동작한다."
tags = ["java"]
categories = ["java"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Keycloak JS Adapter 하나로 초기화하면 로그인/로그아웃/토큰 관리를 직접 구현하지 않아도 SSO가 동작한다.

---

## Keycloak SSO를 왜 쓰는지 감 잡기

여러 애플리케이션을 운영하다 보면 각 앱마다 로그인 로직을 따로 만들어야 한다. 사용자도 매번 따로 로그인해야 하고, 팀은 인증 코드를 중복으로 유지해야 한다. SSO(Single Sign-On)는 한 곳에서 한 번 로그인하면 연결된 모든 서비스에 자동으로 인증이 전파되는 방식이다. Keycloak은 이 역할을 하는 오픈소스 인증 서버로, OAuth 2.0 / OpenID Connect 표준을 따른다.

구글 계정 하나로 유튜브, 지메일, 드라이브를 오가는 것과 같은 원리다.

`핵심 흐름: 브라우저 → Keycloak 서버 → 로그인 → Redirect URI로 복귀 → 토큰 획득 → API 호출`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Realm | Keycloak 안에서 사용자·클라이언트·권한을 묶는 독립 구역 |
| Client | Keycloak에 등록된 애플리케이션 단위 (내 앱 = 하나의 Client) |
| Access Token | 로그인 성공 후 받는 짧은 수명의 인증 증명서 |
| Redirect URI | 로그인 완료 후 사용자를 돌려보낼 내 앱의 URL |
| keycloak.js | Keycloak이 제공하는 브라우저용 JavaScript Adapter |

## 예를 들어 설명하면

순수 JavaScript 환경에서 연동하는 최소 예시다.

```html
<script src="https://<Keycloak 서버>/auth/js/keycloak.js"></script>
<script>
  const keycloak = new Keycloak({
    url: 'https://<Keycloak 서버>/auth',
    realm: 'example-realm',
    clientId: 'example-client'
  });

  keycloak.init({ onLoad: 'check-sso', checkLoginIframe: false })
    .then(authenticated => {
      if (authenticated) {
        console.log('토큰:', keycloak.token);
      }
    });

  document.getElementById('loginBtn').onclick = () => keycloak.login();
  document.getElementById('logoutBtn').onclick = () => keycloak.logout();
</script>
```

`onLoad: 'login-required'`로 바꾸면 앱 진입 시 즉시 로그인 화면을 강제한다.

## 이 단계에서 중요한 판단 기준

Keycloak Admin 콘솔의 **Valid Redirect URIs** 설정이 실제 앱 URL과 정확히 일치하지 않으면 로그인 후 리다이렉트가 막힌다 — 연동 실패의 대부분은 이 설정 불일치에서 온다.

## 한 줄 요약 — 이것만 기억하면 된다

**keycloak.js 하나로 init → login → token 관리까지 끝난다. 핵심 설정은 Realm, Client ID, Redirect URI 세 가지다.**

## 나중에 더 깊게 들어가면

- 액세스 토큰 만료 전 자동 갱신 (`keycloak.updateToken()`)
- React / Vue Adapter 패턴으로 감싸는 법
- CORS 설정 및 HTTPS 운영 환경 구성
- LDAP 연동 / 소셜 로그인 / 2FA 추가

---

**원본:** [Keycloak SSO를 JavaScript로 연동하기](https://memoryhub.tistory.com/441)
