+++
title = "Coolify — 자체 서버에서 Heroku처럼 배포하는 오픈소스 PaaS"
date = "2024-06-12"
description = "Coolify는 내 서버에 설치하는 오픈소스 PaaS다. GitHub에서 코드를 가져다 몇 번의 클릭으로 배포하고, 모니터링과 SSL까지 자동으로 처리해준다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Coolify는 내 서버에 설치하는 오픈소스 PaaS다. GitHub에서 코드를 가져다 몇 번의 클릭으로 배포하고, 모니터링과 SSL까지 자동으로 처리해준다.

---

## Coolify를 왜 쓰는지 감 잡기

PaaS(Platform as a Service)란 서버 설정, 배포 파이프라인, SSL 인증서 등 인프라 작업을 플랫폼이 대신 처리해주는 서비스다. Heroku나 Render 같은 상용 PaaS를 쓰면 편하지만 비용이 빨리 늘어난다.

Coolify는 같은 편의성을 내 서버 위에서 구현한다. VPS 하나에 Coolify를 설치하면 그 서버가 작은 Heroku처럼 동작한다. Git 저장소를 연결하고 배포 버튼을 누르면 빌드, 컨테이너 실행, 도메인 연결, HTTPS 설정까지 자동으로 진행된다.

초보자는 이렇게 이해하면 된다.

`핵심 흐름: GitHub 저장소 연결 → 환경 변수 입력 → 배포 클릭 → 도메인 자동 연결 → 모니터링 대시보드`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PaaS (Platform as a Service) | 인프라 설정 없이 코드만 올리면 실행해주는 클라우드 서비스 형태. |
| 수직 확장 (Vertical Scaling) | 서버 사양을 높이는 방식. CPU나 메모리를 업그레이드하는 것과 같다. |
| 수평 확장 (Horizontal Scaling) | 서버 대수를 늘리는 방식. 같은 앱을 여러 인스턴스로 운영한다. |
| SSL 자동화 | HTTPS 인증서를 Let's Encrypt로 자동 발급·갱신하는 기능. 별도 설정 없이 적용된다. |
| 자체 호스팅 (Self-hosting) | 서비스를 외부 플랫폼이 아닌 내가 소유한 서버에서 직접 운영하는 방식. |

## 예를 들어 설명하면

헬스 트래킹 웹앱을 배포하는 두 가지 경로를 비교한다.

수동 방식으로는 AWS EC2 인스턴스를 만들고, 운영체제를 설치하고, Nginx를 설정하고, 도메인 DNS를 연결하고, Let's Encrypt 인증서를 받고, Git에서 코드를 내려받아 빌드하고, 프로세스 매니저로 실행해야 한다. 각 단계마다 명령어를 직접 입력한다.

Coolify 방식으로는 다음과 같다.

```
1. Coolify 대시보드에서 "New Service" 선택
2. GitHub 저장소 URL 입력
3. 환경 변수 (DATABASE_URL 등) 입력
4. 사용할 도메인 입력
5. "Deploy" 클릭

→ 빌드, 컨테이너 실행, SSL 발급, 도메인 연결 자동 처리
→ 트래픽 급증 시 인스턴스 추가 (설정에 따라 자동)
```

## 이 단계에서 중요한 판단 기준

"상용 PaaS 비용이 부담스럽고 서버를 직접 관리할 의향이 있다면 Coolify가 적합하다. 서버 운영 자체를 맡기고 싶다면 Heroku나 Render 같은 완전 관리형이 낫다."

## 한 줄 요약 — 이것만 기억하면 된다

**Coolify는 내 VPS 위에 설치하는 오픈소스 Heroku다. 코드만 올리면 배포부터 모니터링까지 처리한다.**

## 나중에 더 깊게 들어가면

- Coolify와 Docker Compose, Docker Swarm 연동 설정
- 멀티 서버 환경에서 Coolify로 배포 오케스트레이션하기
- 비용 최적화: 오토스케일링 임계값 설정과 클라우드 비용 모니터링

---

**원본:** [Coolify PaaS — https://memoryhub.tistory.com/280](https://memoryhub.tistory.com/280)
