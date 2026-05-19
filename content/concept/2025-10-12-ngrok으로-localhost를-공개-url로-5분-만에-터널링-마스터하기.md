+++
title = "ngrok으로 localhost를 공개 URL로 — 터널링 기초"
date = "2025-10-12"
description = "`ngrok http 8080` 한 줄이면 로컬 서버가 즉시 외부에서 접근 가능한 HTTPS URL로 공개된다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `ngrok http 8080` 한 줄이면 로컬 서버가 즉시 외부에서 접근 가능한 HTTPS URL로 공개된다.

---

## ngrok을 왜 쓰는지 감 잡기

로컬에서 개발 중인 서버를 외부에서 접근해야 하는 상황이 있다. 웹훅 콜백을 받아야 하거나, 실제 기기에서 API를 테스트하거나, 배포 없이 팀원에게 현재 작업물을 공유해야 할 때다. 이런 상황마다 서버를 배포하거나 공유기에서 포트 포워딩을 설정하는 건 번거롭다.

ngrok은 이 문제를 해결하는 역방향 프록시 터널링 도구다. 로컬 포트와 ngrok 서버 사이에 보안 터널을 만들고, 외부에서 접근 가능한 HTTPS URL을 발급해준다. 포트 포워딩도, 배포도 필요 없다.

`핵심 흐름: 로컬 서버 실행 → ngrok 터널 생성 → ngrok 서버가 외부 요청 수신 → 터널 경유로 로컬에 전달`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 터널링 | 방화벽을 통과해 외부에서 내부 네트워크에 도달할 수 있게 만드는 기술 |
| 역방향 프록시 | 외부 요청을 받아서 내부 서버로 전달하는 중간 서버. ngrok 서버가 이 역할을 한다. |
| 인증 토큰 | ngrok 계정을 로컬 설치에 연결하는 키. 등록하면 세션 시간 제한이 없어진다. |
| Forwarding URL | ngrok이 발급해주는 공개 HTTPS 주소. 이 주소로 외부에서 로컬 서버에 접근한다. |
| 웹 인터페이스 | `http://127.0.0.1:4040`에서 실시간으로 모든 요청·응답을 확인하고 재전송할 수 있는 로컬 UI |

## 예를 들어 설명하면

로컬에서 8080번 포트로 서버가 실행 중이라면:

```bash
ngrok http 8080
```

실행하면 터미널에 다음과 같은 출력이 나온다.

```
Forwarding  https://abc123.ngrok-free.app -> http://localhost:8080
```

`https://abc123.ngrok-free.app`이 외부에서 접근 가능한 주소다. 이 URL로 웹훅을 등록하거나, 팀원에게 공유하면 된다.

무료 플랜은 세션이 끊기면 URL이 바뀐다. 고정 URL이 필요하다면 아래처럼 계정 토큰을 등록한다.

```bash
ngrok authtoken <YOUR_AUTHTOKEN>
```

데이터베이스처럼 HTTP가 아닌 서비스도 터널링할 수 있다.

```bash
ngrok tcp 3306   # MySQL
ngrok tcp 5432   # PostgreSQL
```

단, 데이터베이스 TCP 터널은 인증과 IP 제한을 반드시 설정해야 한다.

## 이 단계에서 중요한 판단 기준

빠른 테스트와 데모에는 ngrok, 고정 도메인과 운영 수준의 안정성이 필요하다면 Cloudflare Tunnel을 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**ngrok은 로컬 포트 하나를 즉시 공개 HTTPS URL로 바꿔주는 개발자용 터널링 도구다.**

## 나중에 더 깊게 들어가면

- ngrok 설정 파일(`ngrok.yml`)로 여러 터널을 한 번에 실행하기
- 커스텀 도메인 연결 (유료 플랜)
- Cloudflare Tunnel과의 기능·비용 비교 및 장기 운영 전략

---

**원본:** [ngrok으로 localhost를 공개 URL로! 5분 만에 터널링 마스터하기](https://memoryhub.tistory.com/849)
