+++
title = "저전력 홈 서버 구축 — 도메인 연결까지 (2025)"
date = "2025-08-05"
description = "Intel N100 미니 PC에 Linux를 설치하고, DDNS 또는 Cloudflare Tunnel로 외부 도메인을 연결하면 월 전기료 몇 백 원으로 개인 서버를 운영할 수 있다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Intel N100 미니 PC에 Linux를 설치하고, DDNS 또는 Cloudflare Tunnel로 외부 도메인을 연결하면 월 전기료 몇 백 원으로 개인 서버를 운영할 수 있다.

---

## 홈 서버를 왜 쓰는지 감 잡기

클라우드 서버는 편리하지만 월 요금이 지속적으로 나간다. 홈 서버는 초기 하드웨어 비용만 들고, 이후에는 전기료만 발생한다. 저전력 미니 PC는 24시간 켜놔도 한 달 전기료가 1~2달러 수준이다.

문제는 가정용 인터넷은 IP가 수시로 바뀐다는 점이다. 고정 IP 없이 외부에서 내 서버에 접속하려면 이 IP 변화를 추적하는 별도 장치가 필요하다. DDNS와 Cloudflare Tunnel이 그 역할을 한다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: 미니 PC 선택 → OS 설치 → 외부 접근 설정 (DDNS 또는 터널) → 도메인 연결`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| DDNS (Dynamic DNS) | 집 IP가 바뀔 때마다 도메인 레코드를 자동으로 업데이트해주는 서비스. |
| 포트 포워딩 | 공유기에서 외부 80/443번 요청을 내부 서버 IP로 전달하도록 설정하는 것. |
| Cloudflare Tunnel | 포트 포워딩 없이 Cloudflare 에이전트가 외부-내부 연결을 중계하는 터널 방식. |
| CNAME 레코드 | 자신의 도메인을 DDNS 호스트 주소로 가리키는 DNS 별칭 설정. |
| Carrier-Grade NAT | ISP가 여러 가구를 하나의 공인 IP로 묶는 구조. 포트 포워딩이 불가능해진다. |

## 예를 들어 설명하면

Intel N100 미니 PC(약 150~200달러)에 Ubuntu Server를 설치한 경우를 기준으로 외부 접근 설정 흐름을 보면 다음과 같다.

**DDNS 방식 (포트 포워딩 필요)**

1. No-IP 또는 DuckDNS에서 무료 계정 생성 후 호스트명 등록 (예: myserver.ddns.net)
2. DDNS 업데이트 클라이언트를 서버에 설치 — IP가 바뀌면 자동 갱신
3. 공유기에서 포트 80, 443을 서버 내부 IP로 포워딩
4. Let's Encrypt certbot으로 HTTPS 인증서 발급

**Cloudflare Tunnel 방식 (포트 포워딩 불필요)**

1. Cloudflare에 도메인 등록 후 네임서버 변경
2. 서버에 `cloudflared` 에이전트 설치 및 인증
3. Cloudflare 대시보드에서 터널 생성 및 도메인 매핑 — TLS는 Cloudflare가 자동 처리

Cloudflare Tunnel은 집 IP를 노출하지 않고 Carrier-Grade NAT 환경에서도 동작한다.

## 이 단계에서 중요한 판단 기준

ISP가 Carrier-Grade NAT를 사용하거나 집 IP 노출이 꺼려진다면 Cloudflare Tunnel을 선택한다. 포트 포워딩이 가능하고 단순한 설정을 원하면 DDNS가 더 빠르다.

## 한 줄 요약 — 이것만 기억하면 된다

**N100 미니 PC + Cloudflare Tunnel 조합이면 포트 포워딩 없이 안전하게 홈 서버에 도메인을 붙일 수 있다.**

## 나중에 더 깊게 들어가면

- Nginx Proxy Manager로 복수 서비스를 서브도메인별로 라우팅하는 방법
- fail2ban과 ufw로 SSH 보안 강화
- WireGuard VPN + 클라우드 VM을 조합한 역방향 프록시 구성

---

**원본:** [Running a low-power home server with domain access (2025) — https://memoryhub.tistory.com/736]
