+++
title = "Coolify — 셀프호스팅 PaaS로 배포를 단순화하는 방법"
date = "2024-06-12"
description = "Coolify는 서버 한 대만 있으면 Git 저장소를 연결해 원클릭으로 애플리케이션을 배포할 수 있는 오픈소스 PaaS다."
tags = ["linux"]
categories = ["linux"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Coolify는 서버 한 대만 있으면 Git 저장소를 연결해 원클릭으로 애플리케이션을 배포할 수 있는 오픈소스 PaaS다.

---

## Coolify를 왜 쓰는지 감 잡기

애플리케이션을 서버에 올리려면 서버 프로비저닝, 도메인 연결, SSL 인증서, 환경 변수 관리, 프로세스 재시작 등 배포 자체와 무관한 작업이 많다. Heroku나 Render 같은 상용 PaaS는 이를 대신해주지만 비용이 크다. Coolify는 자체 서버에 설치하는 오픈소스 PaaS로, 이 인프라 복잡도를 UI 뒤로 숨겨준다. 소규모 팀이나 개인 프로젝트에서 DevOps 인력 없이 배포 파이프라인을 구성할 때 현실적인 선택지다.

초보자는 처음에 이렇게 이해하면 된다.

`핵심 흐름: Git 저장소 연결 → 빌드 명령 설정 → Deploy 클릭 → Coolify가 빌드·실행·서빙 처리`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| PaaS(Platform as a Service) | 서버 OS와 런타임을 관리해주는 중간 계층. 개발자는 코드와 설정만 신경 쓴다 |
| 인프라 추상화 | 서버 세팅, 네트워크 설정, OS 패치 같은 저수준 작업을 플랫폼이 대신 처리하는 것 |
| 원클릭 배포 | Git 커밋 → 빌드 → 실행까지의 과정을 버튼 하나로 트리거하는 워크플로 |
| 내장 서비스 | Coolify가 함께 관리해주는 MySQL, PostgreSQL, Redis 같은 데이터베이스·캐시 인스턴스 |
| 모니터링·로그 | 배포된 앱의 CPU·메모리 사용량과 stdout 로그를 Coolify 대시보드에서 확인하는 기능 |

## 예를 들어 설명하면

Node.js 앱을 Coolify로 배포하는 최소 절차다.

```
1. Coolify 대시보드 → New Resource → Public Git Repository
2. 저장소 URL 입력
3. Build Command:  npm install
   Start Command:  npm start
4. 환경 변수 탭에서 DATABASE_URL 등 추가
5. Services 탭에서 PostgreSQL 인스턴스 추가 (원클릭)
6. Deploy 버튼 클릭
```

Coolify가 코드를 pull하고, Docker 컨테이너로 빌드하고, 리버스 프록시(Traefik)를 통해 외부에 서빙한다. SSL 인증서는 Let's Encrypt로 자동 발급된다.

## 이 단계에서 중요한 판단 기준

Coolify가 적합한 상황은 자체 VPS(가상 서버)를 갖고 있고 상용 PaaS 비용을 줄이고 싶을 때다 — 완전히 관리형 서비스가 필요하거나 팀 규모가 크면 AWS ECS, GCP Cloud Run 같은 관리형 플랫폼이 더 낫다.

## 한 줄 요약 — 이것만 기억하면 된다

**Coolify는 자체 서버에 설치하는 오픈소스 PaaS로, Git 연동과 원클릭 배포로 인프라 설정 없이 앱을 운영할 수 있다.**

## 나중에 더 깊게 들어가면

- Coolify의 내부 동작 — Docker Compose와 Traefik 리버스 프록시 구조
- GitHub Actions와 Coolify 웹훅을 연동한 CI/CD 파이프라인 구성
- 멀티 서버 환경에서 Coolify로 스케일 아웃하기

---

**원본:** [Can you explain how Coolify PaaS simplifies the deployment process for developers? — https://memoryhub.tistory.com/281](https://memoryhub.tistory.com/281)
