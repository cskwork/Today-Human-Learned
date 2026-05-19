+++
title = "Cloudflare Tunnel — 포트 개방 없이 서버를 외부에 공개하는 방법"
date = "2025-10-06"
description = "서버에 `cloudflared` 데몬을 설치하면, 방화벽 포트를 하나도 열지 않고도 외부에서 안전하게 접근 가능한 HTTPS 주소를 얻을 수 있다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> 서버에 `cloudflared` 데몬을 설치하면, 방화벽 포트를 하나도 열지 않고도 외부에서 안전하게 접근 가능한 HTTPS 주소를 얻을 수 있다.

---

## Cloudflare Tunnel을 왜 쓰는지 감 잡기

서버를 외부에 공개하는 전통적인 방법은 두 가지다. 공유기에서 포트를 열거나(포트 포워딩), VPN을 설치하는 것이다. 두 방법 모두 서버의 실제 IP 주소가 외부에 드러난다. IP가 노출되면 DDoS 공격이나 포트 스캐닝의 표적이 된다. 가정용 인터넷은 IP가 수시로 바뀌기도 해서 DDNS 설정도 따로 필요하다.

Cloudflare Tunnel은 이 문제를 뒤집은 방식으로 해결한다. 서버가 먼저 Cloudflare 쪽으로 연결을 걸어두면, 외부 사용자의 요청은 그 터널을 통해 서버에 도달한다. 서버 IP는 절대 외부에 노출되지 않는다.

비유하자면, 집 주소를 공개하는 대신 우체국 사서함을 등록해두고 우체국이 대신 배달해주는 방식이다.

`핵심 흐름: cloudflared 설치 → Cloudflare로 아웃바운드 연결 생성 → 도메인 매핑 → 외부 요청이 터널 경유 전달`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Cloudflare Tunnel | 서버가 먼저 Cloudflare에 연결을 걸어두는 역방향 터널링 서비스. 포트 개방 불필요. |
| cloudflared | 서버에 설치하는 Cloudflare의 경량 데몬. 이것이 아웃바운드 터널을 유지한다. |
| 아웃바운드 전용 연결 | 서버에서 외부로 나가는 연결만 사용. 방화벽은 인바운드를 막아도 동작한다. |
| Zero Trust | Cloudflare 대시보드의 네트워크 보안 메뉴. 여기서 터널을 생성·관리한다. |
| Public Hostname | 터널에 연결할 도메인 주소. `app.mydomain.com → localhost:3000` 형태로 매핑한다. |

## 예를 들어 설명하면

로컬에서 3000번 포트로 개발 서버가 실행 중이라면, 아래 한 줄로 임시 공개 URL을 즉시 얻을 수 있다.

```bash
cloudflared tunnel --url http://localhost:3000
```

실행하면 `https://random-words.trycloudflare.com` 형태의 URL이 생성된다. Cloudflare 계정 없이도 바로 테스트 가능하다.

고정 도메인이 필요하다면 Cloudflare 대시보드에서 Zero Trust > Networks > Tunnels로 이동해 터널을 생성한다. 생성 시 제공되는 토큰으로 서버에서 cloudflared를 실행하면, 이후 대시보드에서 도메인과 로컬 포트를 자유롭게 매핑할 수 있다.

| 구분 | Cloudflare Tunnel | ngrok | 포트 포워딩 |
|---|---|---|---|
| 비용 | 무료 (커스텀 도메인 포함) | 커스텀 도메인 유료 | 무료 |
| IP 노출 | 없음 | 없음 | 있음 |
| 보안 | 매우 높음 (DDoS 보호 포함) | 높음 | 낮음 |
| 설정 복잡도 | 중간 (계정·도메인 필요) | 낮음 | 높음 |

## 이 단계에서 중요한 판단 기준

빠른 일회성 데모라면 ngrok이 더 간단하고, 프로덕션 수준의 고정 도메인과 DDoS 보호가 필요하다면 Cloudflare Tunnel을 선택한다.

## 한 줄 요약 — 이것만 기억하면 된다

**`cloudflared`가 서버에서 Cloudflare로 아웃바운드 연결을 열어두기 때문에, 인바운드 포트를 전혀 열지 않아도 외부 접근이 가능해진다.**

## 나중에 더 깊게 들어가면

- Cloudflare Access를 활용한 터널 앞단 인증(SSO, 이메일 OTP)
- SSH, RDP 같은 비 HTTP 프로토콜 터널링 설정
- cloudflared를 시스템 서비스(systemd)로 등록해 서버 재시작에도 자동 실행하기

---

**원본:** [Cloudflare Tunnel, 포트 개방 없이 서버를 외부에 공개하는 방법](https://memoryhub.tistory.com/836)
