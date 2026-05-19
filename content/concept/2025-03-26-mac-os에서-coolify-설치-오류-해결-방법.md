+++
title = "Mac OS에서 Coolify 설치 오류 해결 방법"
date = "2025-03-26"
description = "Mac OS는 리눅스가 아니기 때문에 Coolify 공식 설치 스크립트가 작동하지 않으며, Docker Desktop을 통한 컨테이너 실행이 유일한 현실적 해결책이다."
tags = ["concept"]
categories = ["concept"]
draft = false
ShowToc = true
TocOpen = false
+++

> **TL;DR**
> Mac OS는 리눅스가 아니기 때문에 Coolify 공식 설치 스크립트가 작동하지 않으며, Docker Desktop을 통한 컨테이너 실행이 유일한 현실적 해결책이다.

---

## Coolify를 왜 쓰는지 감 잡기

Coolify는 Heroku나 Railway 같은 PaaS(서비스형 플랫폼)를 자기 서버에 직접 올려 쓸 수 있는 오픈소스 도구다. 깃 저장소를 연결하면 코드를 자동으로 빌드하고 배포한다. 주 타겟은 리눅스 서버인데, 개발자가 로컬 맥에서 먼저 테스트해보려다 오류를 만나는 경우가 많다.

공식 설치 스크립트는 `/etc/os-release` 파일을 읽어 리눅스 배포판을 감지한다. Mac OS에는 이 파일이 없으므로 `No such file or directory` 오류와 함께 스크립트가 중단된다.

`핵심 흐름: Mac OS → Docker Desktop 설치 → Coolify 컨테이너 실행 → localhost:8000 접속`

## 외워야 할 핵심 용어 5개

| 용어 | 초보자식 설명 |
|---|---|
| Coolify | 내 서버에서 직접 운영하는 자체 호스팅 배포 플랫폼 |
| Docker Desktop | Mac/Windows에서 리눅스 컨테이너를 실행할 수 있는 앱 |
| 컨테이너 | 앱과 그 실행 환경을 한 상자에 담아 어디서든 동일하게 실행하는 단위 |
| 볼륨(Volume) | 컨테이너가 꺼져도 데이터를 유지하는 저장 공간 |
| Docker Compose | 여러 컨테이너를 하나의 설정 파일로 묶어 한 번에 실행하는 도구 |

## 예를 들어 설명하면

Docker Desktop이 설치되어 있다면 터미널에서 아래 명령 하나로 Coolify를 실행할 수 있다.

```bash
docker run -d \
  --name coolify \
  -p 8000:8000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v coolify-db:/app/db \
  -v coolify-backup:/app/backup \
  coollabsio/coolify:latest
```

실행 후 브라우저에서 `http://localhost:8000`으로 접속하면 Coolify 초기 설정 화면이 나타난다. 컨테이너가 여러 개 필요하거나 재시작 정책을 세밀하게 제어하려면 `docker-compose.yml`을 작성해 `docker-compose up -d`로 올리는 방식을 쓴다.

## 이 단계에서 중요한 판단 기준

Mac에서 Coolify를 테스트할 목적이라면 Docker Desktop 경유가 유일하게 공식 지원되는 방법이고, 실제 운영은 리눅스 서버에서 진행해야 한다.

## 한 줄 요약 — 이것만 기억하면 된다

**Mac OS에서 Coolify 설치 스크립트가 실패하는 이유는 리눅스 전용 파일(`/etc/os-release`)을 참조하기 때문이며, Docker Desktop으로 컨테이너를 직접 실행하면 해결된다.**

## 나중에 더 깊게 들어가면

- Docker 볼륨 관리와 데이터 영속성
- Coolify를 실제 리눅스 서버(VPS)에 배포하는 방법
- Coolify에서 깃 저장소 연동 및 자동 배포 파이프라인 구성

---

**원본:** Mac OS에서 Coolify 설치 오류 해결 방법 — https://memoryhub.tistory.com/532
