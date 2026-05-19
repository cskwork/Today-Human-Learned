# Nginx — 핵심 설정 빠르게 이해하기

> **TL;DR**
> Nginx는 클라이언트 요청을 받아 파일을 직접 서빙하거나 백엔드 서버로 중계하는 웹 서버이며, 몇 가지 블록 구조만 익히면 대부분의 상황을 처리할 수 있다.

---

## Nginx를 왜 쓰는지 감 잡기

Nginx(엔진엑스)는 웹 서버이자 리버스 프록시다. 사용자의 브라우저가 요청을 보내면 Nginx가 가장 먼저 받아서, 정적 파일(HTML·이미지)은 직접 돌려주고 동적 처리가 필요한 요청은 뒤쪽 애플리케이션 서버(Node.js, Python 등)로 넘긴다. 트래픽이 몰릴 때 여러 서버에 부하를 나눠주는 로드 밸런서 역할도 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 브라우저 요청 → Nginx → (정적 파일 직접 응답 | 백엔드 서버로 전달)`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| `server` 블록 | 하나의 가상 호스트(도메인·포트) 설정 단위 |
| `location` 블록 | URL 경로별로 다른 동작을 지정하는 구역 |
| `proxy_pass` | 요청을 백엔드 서버 주소로 중계하는 지시어 |
| `upstream` | 요청을 분산할 백엔드 서버 그룹을 이름으로 묶는 블록 |
| `return 301` | 영구 리다이렉트 — 브라우저에게 "이 주소로 다시 요청하라"고 알림 |

## 예를 들어 설명하면

Node.js 앱(포트 3000)을 외부에 노출할 때 가장 기본적인 패턴이다.

```nginx
upstream app {
    server 127.0.0.1:3000;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://app;
    }
}

# HTTP → HTTPS 강제 리다이렉트
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

로드 밸런싱이 필요하면 `upstream` 블록에 서버를 추가하기만 하면 된다.

```nginx
upstream app {
    server 127.0.0.1:3000;
    server 127.0.0.1:4000;
}
```

## 이 단계에서 중요한 판단 기준

설정 변경 후에는 반드시 `sudo nginx -t`로 문법 오류를 확인하고, `sudo systemctl reload nginx`로 서비스 중단 없이 적용한다.

## 한 줄 요약 — 이것만 기억하면 된다

**`server` 블록으로 도메인을 잡고, `location` 블록으로 경로를 분기하고, `proxy_pass`로 백엔드에 넘긴다.**

## 나중에 더 깊게 들어가면

- SSL/TLS 인증서 설정 (Let's Encrypt + Certbot 자동화)
- WebSocket 연결을 위한 `Upgrade` 헤더 처리
- `proxy_cache`를 이용한 응답 캐싱 전략

---

**원본:** [Nginx CheatSheet — https://memoryhub.tistory.com/55](https://memoryhub.tistory.com/55)
