+++
title = "SSH PermitRootLogin — root 직접 로그인을 막아야 하는 이유"
date = "2024-06-11"
description = "`PermitRootLogin`을 명시하지 않으면 SSH 서버가 버전마다 다른 기본값을 적용해 의도치 않은 root 접근이 열릴 수 있으므로, 반드시 명시적으로 설정해야 한다."
tags = ["security"]
categories = ["security"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> `PermitRootLogin`을 명시하지 않으면 SSH 서버가 버전마다 다른 기본값을 적용해 의도치 않은 root 접근이 열릴 수 있으므로, 반드시 명시적으로 설정해야 한다.

---

## PermitRootLogin을 왜 신경 써야 하는지 감 잡기

서버에 SSH로 접속할 때, root 계정으로 직접 로그인하는 것은 마스터 키로 건물 정문을 여는 것과 같다. 침입자가 한 번 성공하면 시스템 전체를 가져간다. 대부분의 보안 지침이 root 직접 로그인을 금지하거나 공개 키 인증으로만 제한하는 이유다.

`/etc/ssh/sshd_config` 파일에 `PermitRootLogin` 항목이 없거나 주석 처리돼 있으면, SSH 서버는 버전에 따라 다른 기본값을 사용한다. 이것이 경고의 원인이다.

처음에는 이렇게 이해하면 된다.

`핵심 흐름: sshd_config 편집 → PermitRootLogin 값 명시 → SSH 서비스 재시작 → 정책 적용`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| sshd_config | SSH 서버의 동작 방식을 정의하는 설정 파일 (`/etc/ssh/sshd_config`) |
| PermitRootLogin | root 계정의 SSH 로그인 허용 여부와 방식을 지정하는 지시어 |
| prohibit-password | 비밀번호 로그인은 차단하고 공개 키 인증만 허용하는 설정값 |
| 공개 키 인증 | 비밀번호 대신 키 쌍(공개 키/비밀 키)으로 신원을 증명하는 방식 |
| systemctl restart sshd | 설정 변경을 서버에 즉시 반영하는 명령 |

## 예를 들어 설명하면

가장 일반적으로 권장하는 설정은 `prohibit-password`다. root로 로그인은 허용하되, 비밀번호 방식은 막는다.

```bash
# 1. 설정 파일 열기
sudo nano /etc/ssh/sshd_config

# 2. 아래 줄을 추가하거나 주석 해제
PermitRootLogin prohibit-password

# 3. 저장 후 SSH 서비스 재시작
sudo systemctl restart sshd
```

더 강하게 막으려면 `no`로 설정한다. 이 경우 root는 SSH로 절대 로그인할 수 없고, 일반 계정으로 접속 후 `sudo`를 써야 한다.

## 이 단계에서 중요한 판단 기준

운영 서버라면 `PermitRootLogin no`가 기본값이어야 한다 — 만약 root 접근이 반드시 필요하다면 일반 계정 + sudo 조합을 쓰고, root 직접 로그인은 열어 두지 않는다.

## 한 줄 요약 — 이것만 기억하면 된다

**`PermitRootLogin`은 반드시 명시적으로 설정해야 하며, 운영 환경에서는 `no` 또는 `prohibit-password`가 기본값이 돼야 한다.**

## 나중에 더 깊게 들어가면

- `AllowUsers` / `AllowGroups`: 특정 계정만 SSH 접근을 허용하는 추가 제한 방법
- `fail2ban`: 반복 로그인 시도를 자동으로 차단하는 브루트포스 방어 도구
- SSH 포트 변경: 기본 22번 포트를 바꿔 자동화 스캔을 줄이는 방법과 그 한계

---

**원본:** PermitRootLogin SSH Security Policy Explained — https://memoryhub.tistory.com/268
