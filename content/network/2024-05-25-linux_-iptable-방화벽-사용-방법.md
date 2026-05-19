+++
title = "Linux iptables — 방화벽 규칙을 직접 제어하는 방법"
date = "2024-05-25"
description = "iptables는 리눅스 커널의 패킷 필터링 도구로, 체인(INPUT·OUTPUT·FORWARD)에 규칙을 추가해 들어오고 나가는 트래픽을 허용하거나 차단한다."
tags = ["network"]
categories = ["network"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> iptables는 리눅스 커널의 패킷 필터링 도구로, 체인(INPUT·OUTPUT·FORWARD)에 규칙을 추가해 들어오고 나가는 트래픽을 허용하거나 차단한다.

---

## iptables를 왜 쓰는지 감 잡기

리눅스 서버는 외부와 끊임없이 패킷을 주고받는다. 방화벽이 없다면 어떤 포트든 누구든 접근할 수 있다. iptables는 커널 레벨에서 이 패킷 흐름에 개입해 "허용할 것"과 "차단할 것"을 규칙으로 정의한다. 클라우드 환경의 보안 그룹(Security Group)이 같은 역할을 더 추상화해 제공하지만, 온프레미스 서버나 세밀한 제어가 필요할 때 iptables를 직접 다루게 된다.

`핵심 흐름: 외부 패킷 → 커널 → iptables 체인 규칙 순차 검사 → ACCEPT / DROP / REJECT`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| 체인(Chain) | 패킷이 통과하는 관문. INPUT(들어옴), OUTPUT(나감), FORWARD(통과) 세 가지가 기본 |
| `-A / -I` | 규칙 추가 방식. `-A`는 맨 아래에 붙이고, `-I`는 맨 위에 끼워 넣는다 |
| `-j ACCEPT / DROP` | 규칙에 걸린 패킷의 처리 결과. DROP은 응답 없이 버리고, REJECT는 거절 메시지를 돌려준다 |
| `--dport` | 목적지 포트 번호. 어떤 서비스(SSH=22, HTTP=80)를 열지 지정할 때 쓴다 |
| 상태 추적(conntrack) | ESTABLISHED(기존 연결), NEW(새 연결), RELATED(연관 연결)로 패킷을 분류해 정교한 규칙을 가능하게 하는 모듈 |

## 예를 들어 설명하면

서버를 처음 설정할 때 쓰는 기본 방화벽 스크립트다. 규칙을 위에서 아래로 순서대로 검사하므로, SSH를 먼저 허용해야 원격 접속이 끊기지 않는다.

```bash
# 기존 규칙 전체 초기화
iptables -F

# SSH 원격 접속 허용 (이걸 먼저 설정해야 잠기지 않음)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# 웹 서비스 허용
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# 루프백(자기 자신) 허용
iptables -A INPUT -s 127.0.0.1 -j ACCEPT

# 기존에 맺어진 연결은 유지
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT

# 나머지 모든 인바운드 트래픽 차단
iptables -A INPUT -j REJECT

# 설정 저장
service iptables save
```

## 이 단계에서 중요한 판단 기준

체인 규칙은 위에서 아래로 첫 번째 일치 규칙이 적용된다. 특정 포트를 ACCEPT로 열었더라도 그 위에 해당 IP를 DROP하는 규칙이 있으면 무시된다. 규칙이 의도대로 동작하지 않을 때는 순서를 먼저 확인한다.

## 한 줄 요약 — 이것만 기억하면 된다

**iptables는 규칙 순서가 전부다. SSH 허용을 맨 먼저 추가하고, 기본 정책을 DROP으로 닫아라.**

## 나중에 더 깊게 들어가면

- NAT 테이블: PREROUTING·POSTROUTING을 이용한 포트 포워딩과 Masquerade
- ipset으로 대량 IP 목록을 효율적으로 차단하는 방법
- nftables: iptables의 후속 도구로 현대 리눅스 배포판에서 기본으로 채택되는 추세

---

**원본:** [Linux: Iptable 방화벽 사용 방법 — https://memoryhub.tistory.com/85](https://memoryhub.tistory.com/85)
